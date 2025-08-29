# secrets

this project uses Doppler to manage secrets, with a single project with 3 envs: dev, preview and production. dev is the env already selected and implicing in doppler calls.

in typescript never use process.env directly, instead find the closes `env.ts` file that export a env object (this file should already exist). so the env can be used type safely and i can clearly see which secrets are available and need to be added.