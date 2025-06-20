# How to use your own MCP tool in veRL

### Configuration

1. Create a configuration file like `examples/sglang_multiturn/config/tool_config/mcp_tool_config.yaml`

   ```yaml
   tools:
   	# specify your tool class name, default is verl.tools.mcp_search_tool.MCPBaseTool
     - class_name: verl.tools.mcp_search_tool.MCPSearchTool
       config:
         rate_limit: 120
         timeout: 120
         type: mcp
       mcp:
         # specify the json file of mcp server
         mcp_servers_config_path: ./mcp_server.json
         # [optional] this allows you only use a part of tools in mcp server, if you want to use all the tools provided by mcp server, just delete the attribute. 
         tool_selected_list: 
           - tavily_search_tool
   ```

2. Create the json file of mcp server like `examples/sglang_multiturn/config/tool_config/mcp_server.json`. SSE and STDIO are all supported. You can reference the documentation from https://gofastmcp.com/clients/transports#configuration-based-transports. Surely, you can use your own mcp server or use servers from https://glama.ai/mcp/servers/@tavily-ai/tavily-mcp.

   ```json
   {
       "mcpServers": {
           "Tavily SSE": {
               "url": "your_tavily_expert_url",
               "auth_token": "your_tavily_api_token"
           }
         "Tavily STDIO": {
           "command": "npx",
           "args": ["-y", "tavily-mcp@0.2.1"],
           "env": {
             "TAVILY_API_KEY": "your_tavily_api_token"
           }
         }
       }
   }
   ```

### Optional

Since the content formats returned by the MCP server vary, to allow users to parse the results themselves, users can implement a class similar to `MCPSearchTool` and override the `_parse_tool_result` interface.

```
class MCPYourTool(MCPBaseTool):
    def __init__(self, config: dict, tool_schema: OpenAIFunctionToolSchema):
        super().__init__(config, tool_schema)

    def _parse_tool_result(self, content: list) -> Tuple[str, dict]:
        pass
```

### Note

Currently, MCP calls are implemented based on function calls, and only the use of MCP tools is supported for now. In the future, the use of resources and prompts will be supported to enable pipeline processing among tools, resources, and prompts.
