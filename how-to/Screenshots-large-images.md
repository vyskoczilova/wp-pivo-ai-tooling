# Smart Screenshot Strategy to avoid Claude freezing on large images:

- First, check page height: `mcp__chrome-devtools__evaluate_script` with `() => document.documentElement.scrollHeight`
- **Short pages (<8000px)**: Take one full-page screenshot with `fullPage=true`
- **Long pages (>8000px)**: DO NOT take full-page screenshots. Instead:
   - Take targeted viewport screenshot of the new component area only
   - Scroll to component location before taking screenshot
   - Or use `take_screenshot` with specific `uid` parameter to capture just the component