### 📎 Lab 18 - Run-Commands and deploying the frontend

###### ⏰ Estimated time: 15-20 minutes

#### 📚 Learning outcomes:

- Understand how to create custom builders via the "run-commands" workspace builder
- Explore real-world usages of "run-commands" by creating a frontend "deploy" builder
- Learn how to expose custom ENV variables to Nx

#### 🏋️‍♀️ Steps :

1. Make sure you are on the `master` branch
2. We'll use a CLI tool called [Surge](https://surge.sh/) to statically deploy the frontend:

    ```bash
    yarn add surge
    ```
   
4. Get the surge token (you'll need to create an account with an email and password):

    ```
    yarn surge token
    ```

    ☝️ Copy the token you get

6. Let's use the Surge CLI to deploy our project:

    ```
   # make sure the project is built first - and we have something in dist
   nx build store
   # use surge to deploy whatever assets are in dist/apps/store
   yarn surge dist/apps/store https://<chose-some-unique-url-123>.surge.sh --token <your-surge-token>
   ```
   
    ⚠️ Make sure you chose a **unique value** for your domain above, otherwise
    it will fail as you won't have permission to deploy to an existing one.
    
    ⚠️ You should see surge deploying to your URL - if you click you'll see just the header though, because it doesn't have a server yet to get the games from.

3. Let's now abstract away the above command into an Nx target.
Generate a new **"deploy"** target using the `@nrwl/workspace:run-commands` schematic:
    - under the `store` project
    - the "command" will be the same as the one you typed in the previous step
    
    <details>
    <summary>🐳 Hint</summary>
    
    Consult the run-commands schematic docs [here](https://nx.dev/latest/angular/plugins/workspace/schematics/run-commands)
    </details>

4. Use Git to inspect the changes in `workspace.json` and try to deploy the store using Nx! 
5. We're now storing the surge token in `workspace.json`. We don't want to check-in this file
and risk exposing this secret token. Also, we might want to deploy to different domains depending on the
 environment. Let's move these out:

    📁 Create a new file `apps/store/.local.env`

    🔒 And let's add your secrets to it
    ```
    SURGE_TOKEN=<your-surge-token>
    SURGE_DOMAIN=https://<some-unique-url-123>.surge.sh
    ```
   
   ✅ Finally, update your "deploy" command, so that it loads the values from the ENV,
   using the `${ENV_VAR}` syntax.

    <details>
    <summary>🐳 Hint</summary>
    
    ```bash
   surge dist/apps/store ${SURGE_DOMAIN} --token ${SURGE_TOKEN} 
   ```
    </details>

6. Since we're deploying, it's a good idea to make sure the store is built for production - we did that in one of the initial labs.
    <details>
    <summary>🐳 Hint</summary>
    
    Consult the [the solution](SOLUTION.md) if you don't remember how.
    </details>

 
 7. Now invoke the deploy target again, and check if it all still works.
 
     Note for Windows users: at this the command might fail, as we're trying to access env variables the Linux-way.
     To make it pass:
     
     ```
    nx generate run-commands windows-deploy --project=store --command="surge dist/apps/store %SURGE_DOMAIN% --token %SURGE_TOKEN%"
    nx windows-deploy store
    ```
    
---

❓ We did not load those environment variables into the deploy process anywhere. 
We just added a `.local.env` file. How does that work?

Nx [automatically picks up](https://nx.dev/latest/angular/cli/overview#loading-environment-variables) any `.env` or `.local.env` files in your workspace,
and loads them into processes invoked for that specific app.

---

🎓If you get stuck, check out [the solution](SOLUTION.md)

---

[➡️ Next lab ➡️](../lab19/LAB.md)