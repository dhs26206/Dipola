# Dipola

A **Node.js Deployment Platform** made by me, on which users can deploy **frontend React apps** and also **backend Express servers**.

## Details

This **Frontend** is pretty simple and a basic React app made with **Vite**. You can check it out using this [link](https://example.com)

The **Backend** is made in **Express** with integration of **MongoDB** as a database and **GitHub OAuth** as the authentication system. You can access the backend source code using this [link](https://example.com)

## How it Works

The backend uses the power of the **Linux system**, utilizing various services offered by Linux like user creation, permission handling, daemon services, tmux, etc.

This project could be made using **Docker**, but it consumes more memory and space, so I chose this approach.

Whenever a user wants to deploy, the platform takes their repo and creates a separate user on the system for isolation purposes. After creating the user, it restricts that user’s access to only read and write within its home directory.

Further, the user’s access is also limited by restricting all commands except **npm**.

- **If the repo is a Frontend Project** (e.g., a React app), it asks the user for the build command (e.g., `npm run build`) and the build directory (e.g., `build` for a simple React app or `dist` for a Vite project).
- **If the repo is a Backend Project**, it only asks the user for the run command, then executes that command after installing dependencies.

During deployment, it also stores logs and has the ability to display those logs in the frontend.

## Deployment Flow (Frontend)

1. User authenticates using **GitHub OAuth** (with access to their repos).
2. A cookie is provided by the backend upon successful authentication (uses **express-session** and **Passport.js** for this).
3. The user is shown their repos, from which they select one.
4. Upon selection, the user inputs the **build command** and **build directory**.
5. A deployment request is sent to the backend with repo details, and the frontend is provided an ID (used to check deployment status and view logs).
6. The server downloads that repo (in `.zip` format).
7. After unzipping, a new user is created (randomly named) and files are transferred to that user's home directory.
8. Dependencies are installed using **pnpm** (limited to 50%).
9. The frontend is built using the provided command (e.g., `npm run build`).
10. **Nginx** is configured to serve this build directory with a custom link (e.g., `abcd.example.com`).
11. A **webhook** is added, enabling automatic redeployment in case of a push.
12. Done.

## Deployment Flow (Backend)

1. User authenticates using **GitHub OAuth** (with access to their repos).
2. A cookie is provided by the backend upon successful authentication (uses **express-session** and **Passport.js** for this).
3. The user is shown their repos, from which they select one.
4. The user inputs the **run command**.
5. Make sure to set `process.env.PORT` before deployment. See more information [here](https://developerport.medium.com/understanding-process-env-port-in-node-js-e09aef80384c)
6. A deployment request is sent to the backend with repo details, and the frontend is provided an ID (used to check deployment status and view logs).
7. The server downloads that repo (in `.zip` format).
8. After unzipping, a new user is created (randomly named) and files are transferred to that user's home directory.
9. Dependencies are installed using **pnpm** (limited to 50%).
10. A new `tmux` session is created and the run command is executed there, with a port assigned to it.
11. **Nginx** is configured to act as a reverse proxy to this port, accessible via the custom link (e.g., `abc.example.com`).
12. A **webhook** is added, enabling automatic redeployment in case of a push.
13. Done.

## More Info

A **daemon** runs in the background, checking every 5 seconds for **CPU** and **memory usage** of each user. If any user exceeds 5% usage (for both CPU and memory) for more than 1 minute, it kills all processes associated with that user.

## Features

- **GitHub OAuth** for repo access and user management.
- Terminal configuration for each user restricts access to critical sections, with no **sudo** access allowed.
- **Nginx** is configured with **HTTPS** enabled.
- **Webhook** feature for continuous deployment in case of new pushes.

## More Improvements (Which I Want but Can’t Implement Yet)

- Currently, there is no feature that limits the **bandwidth of each user** (e.g., up to 5 Mbps).
- Currently, it doesn’t limit the **directory size** of each user (users may download large files and overload the server).
- I haven’t been able to limit **port access** (I tried using `iptables` but am not sure why it isn’t working).

## Try and Testing

This project is heavily embedded in **Linux features** and cannot be tested on a **Windows machine**.

> I myself took a $28 per month machine on **DigitalOcean** to develop this (bill went to $15, lol).  
> Since it involves **SSL certificates** (for configuring **Nginx**), you must have a domain to try it out.

## Credits

Special thanks to **GitHub Student Developer Pack** and **DigitalOcean** for providing enough resources to turn my idea into reality. Also, thanks to **ChatGPT** (for guiding my approach) and **v0.dev** (for easing my anxiety about making the frontend).
