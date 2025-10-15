# WordPress a WooCommerce MCP

[WooCommerce dokumentace](https://developer.woocommerce.com/docs/features/mcp/#connecting-to-the-mcp-server)

```json
"mcpServers": {
  "wordpress-mcp": {
    "command": "npx",
    "args": [
      "-y",
      "@automattic/mcp-wordpress-remote@latest"
    ],
    "env": {
      "WP_API_URL": "https://example.com/",
      "JWT_TOKEN": "<vygenerovaný token>",
      "LOG_LEVEL": "3",
      "LOG_FILE": "/tmp/wordpress-mcp.log"
    }
  },
  "wordpress-woocommerce-localhost": {
    "command": "npx",
    "args": [
        "-y",
        "@automattic/mcp-wordpress-remote@latest"
    ],
    "env": {
        "WP_API_URL": "https://127.0.0.1:32786/",
        "JWT_TOKEN": "<vygenerovaný token>",
        "OAUTH_CALLBACK_PORT": "8080",
        "NODE_TLS_REJECT_UNAUTHORIZED": "0",
        "CUSTOM_HEADERS": "{\"X-MCP-API-Key\": \"<consumer key>:<consumer_secret>\"}"
        }
    },
},
```

`WP_API_URL` - vaše URL, pokud je na localhostu, je nutné ji zadat přes port (soubor `.hosts` není schopný Node.js podproces zavolat)

`OAUTH_CALLBACK_PORT` - možnost vybrat jiný port, pokud je defaultní vyplněný