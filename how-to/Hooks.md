# Claude Code hooks

Notifikace při dokončení úkolu a při doplnění inputu (součástí `settings.json`).

`terminal-notifier` - notifikace pro MacOS, https://www.npmjs.com/package/terminal-notifier

```json
{
	"hooks": {
		"Stop": [
			{
				"matcher": "",
				"hooks": [
					{
						"type": "command",
						"command": "terminal-notifier -title \"Claude Code\" -message \"I've done my job.\" -sound \"Blow\""
					}
				]
			}
		],
		"Notification": [
			{
				"matcher": "",
				"hooks": [
					{
						"type": "command",
						"command": "terminal-notifier -title \"Claude Code\" -message \"I need your attention.\" -sound \"Sosumi\""
					}
				]
			}
		]
	}
}
```