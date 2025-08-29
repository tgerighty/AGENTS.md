## ai sdk

I use the vercel ai sdk to interact with LLMs, also know as the npm package `ai`. never use the openai sdk or provider specific sdks, always use the vercel ai sdk, npm package `ai`. streamText is preferred over generateText, unless the model used is very small and fast and the current code doesn't care about streaming tokens or showing a preview to the user. `streamObject` is also preferred over generateObject.

ALWAYS fetch the latest docs for the ai sdk using this url with curl:
https://gitchamber.com/repos/vercel/ai/main/files

use gitchamber to read the .md files using curl

You can swap out the topic with text you want to search docs for. You can also limit the total results returned with the param token to limit the tokens that will be added to the context window