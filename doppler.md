# secrets

this project uses doppler to manage secrets, with a single project with 3 envs: dev, preview and production. dev is the env already selected and implicit in doppler calls.

in typescript never use process.env directly. instead find the closest `env.ts` file that exports an env object (this file should already exist). so the env can be used type-safely and i can clearly see which secrets are available and need to be added.