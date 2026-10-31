# Spendpoint plugin

Receipts in, answers out. [Spendpoint](https://spendpoint.ai) is an expense ledger with no app of its own: you show a receipt to the assistant you already use, the assistant records it with every line in its own currency, and the sums you ask for come from the service, never from the assistant's arithmetic.

This repository is the plugin package for Cursor and Grok Bot: the manifest, the connection to the hosted server, and the skill that tells the assistant how to record a receipt. It follows the [Agent Plugins](https://agent-plugins.org) standard; `.cursor-plugin/plugin.json` repeats the manifest in the form the Cursor directory reads. The service itself runs at spendpoint.ai; nothing runs on your machine.

## Install

From the marketplace, add the Spendpoint plugin. The first time the assistant calls a tool, the service's consent page opens: sign in, or press "New here?" to create an account with your email address, open the mailed link to verify it, come back and press Allow. The address is used to verify the account and reset the password; the service stores a keyed hash of it, never the address.

Without the marketplace, add the server by its URL in your MCP settings:

```json
{
  "mcpServers": {
    "spendpoint": {
      "url": "https://spendpoint.ai/mcp"
    }
  }
}
```

The server speaks MCP over Streamable HTTP under OAuth 2.1 with PKCE; the client registers itself, no key is needed.

## Try it

- "Here is my receipt. Log it in Spendpoint." with a photo of a receipt.
- "How much did I spend on groceries this month, and where?"
- "Show me the last receipt in full."
- "Export my ledger as CSV."

What the service does with your data is in the [privacy policy](https://spendpoint.ai/privacy); the [docs](https://spendpoint.ai/docs) describe every tool, and the [guide](https://spendpoint.ai/guide) says what you can ask for.

## Contents

| File | What it is |
|---|---|
| `plugin.json` | The manifest. |
| `mcp.json` | The hosted server, `https://spendpoint.ai/mcp`, over Streamable HTTP. |
| `skills/spendpoint/SKILL.md` | How to record a receipt, correct an entry, answer questions and handle budgets. |
| `icon.png` | The icon, 512 pixels square. |

This package is under the MIT licence. Use of the service is under its [terms](https://spendpoint.ai/terms). Support: support@spendpoint.ai.
