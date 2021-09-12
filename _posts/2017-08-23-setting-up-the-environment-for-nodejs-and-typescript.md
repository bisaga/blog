---
layout: post
title: "Setting up the Environment for Node.js and TypeScript"
date: "2017-08-23"
categories: 
  - "javascript"
  - "nodejs"
  - "programming"
  - "typescript"
tags: 
  - "javascript"
  - "nodejs"
  - "typescript"
  - "visual-studio-code"
---

To be productive as a programmer the write/run/debug cycle should be as fast as possible.

Because I am a little spoiled by angular-cli setup for client side web development I search for similar experience in the server side. It means that transpiling the typescript to javascript and node server restarts should be automatic and with hassle free debugging. [![](/assets/images/write_run_debug_cylce.png)](http://bisaga.com/blog/wp-content/uploads/2017/08/write_run_debug_cylce.png)

## Install prerequisites

To create basic node application in the current folder use this command :

npm init

Install additional features needed for the application:

npm install typescript --save-dev
npm install @types/node --save-dev
npm install nodemon --save-dev

If you wish to write with express framework you will need additional libs:

npm install express --save
npm install @types/express --save-dev

 

## The structure of the project folder

Server application is running in completely different project (folder) as angular client application. To develop client and server I will run two instances of vscode editors, each open corresponding folder.

The server folder contains this base structure:

[![](/assets/images/2017-08-24-00_35_19-index.ts-—-wodia-node-—-Visual-Studio-Code-260x300.png)](http://bisaga.com/blog/wp-content/uploads/2017/08/2017-08-24-00_35_19-index.ts-—-wodia-node-—-Visual-Studio-Code.png)

I created **src** sub-folder where all source files will reside. For now I put index.ts file in it.

## Setup typescript compiler

Create tsconfig.json file in the root folder of the project :

{
    "compilerOptions": {
        "target": "es2017",
        "module": "commonjs",
        "moduleResolution": "node",
        "noEmitOnError": true,
        "noImplicitAny": true,
        "experimentalDecorators": true,
        "sourceMap": true,
        "baseUrl": ".",
        "paths": {
            "\*":\[ 
                "src/types/\*"
            \]
        },
        "sourceRoot": "src",
        "outDir": "dist"
    },
    "typeRoots": \[
        "node\_module/@types"
    \],
    "include": \[
        "src/\*\*/\*"
    \],
    "exclude": \[
        "dist",
        "node\_modules"
    \]
}

## Compile and run the node application

Transpile (compile) typescript into javascript and watch any changes (use local tsconfig.json configuration):

tsc --watch

Start node with debug (inspect) and in auto restart mode (nodemon) :

nodemon --inspect ./dist/index.js --watch dist

### Create combined **npm command in package.json file**

To specify multiple commands in the npm  under one script command, divide them with && character. Let's create a "server" command:

"server": "tsc --watch | nodemon --inspect ./dist/index.js --watch dist",

-  "**tsc**": start typescript compiler in watch mode,
-  "**|**" : pipe character allow to add aditional command in same script
- "**nodemon**":  this command will start and restart "node" server after each detected change made by tsc compiler. Nodemon watch destination folder because it need to restart only after javascript files are changed (after transpile).

Before running nodemon for the first time in the project, you need to have compiled index.js at the required place ("dist folder") so you need to compile at least first time the whole application manually (run tsc from the command line). After that initialization the watchers will watch for changes in source file automatically.

#### Run combined command from the terminal:

npm run server

Be aware of the small problem, if you ran the server inside vscode terminal window and then close the editor after that, without canceling the running process (with Ctrl+C), the node server will not automatically stop. You will need to find it between the running processes and stop it manually. To eliminate this obstacle create task and run combined command as task.

## Tasks

To ease start and stop of "npm run server" command and to have better control over all started processes, create a task and a **task runner** for the "server" command.

if you do not have ".vscode" folder  and tasks.json file yet you create it with /Tasks/Configure tasks menu option.

Inside tasks.json file (in .vscode folder) define the command in that form (use version 0.1.0, the version 2.0.0 has [problem with task termination on exiting](https://github.com/Microsoft/vscode/issues/35643) editor):

{
    "version": "0.1.0",
    "isShellCommand": false,
    "showOutput": "always",
    "tasks": \[
        {
            "taskName": "run server",
            "isBackground": true,
            "showOutput": "always",
            "promptOnClose": true,
            "command": "npm",
            "args": \[
                "run", 
                "server"
            \]
        }
}

To run the task in vscode editor, open **Tasks** **menu**, open **Run task** and select **run server** task.

When you want to terminate the task, it means that stop will terminate the tsc instance and all node instances started by the "server" command.  From the **Tasks menu** select  **Terminate task...** .

If you close the editor with **File/Exit** , the program should also ask to terminate still running task.

[![](/assets/images/2017-10-05-22_26_02-tasks.json-database-Visual-Studio-Code.png)](http://bisaga.com/blog/wp-content/uploads/2017/08/2017-10-05-22_26_02-tasks.json-database-Visual-Studio-Code.png)

If you omit "promptOnClose" setting you will not be interrupted at the exit from the editor and the processes will close as expected.

 

### Keyboard shortcuts

Find command (F1)  "**Open keyboard shortcuts file**" and add definition to it.

// Place your key bindings in this file to overwrite the defaults
\[
    {
        "key": "ctrl+t",
        "command": "workbench.action.tasks.terminate",
        "args": "run server"
    },
    {
        "key": "ctrl+r",
        "command": "workbench.action.tasks.runTask",
        "args": "run server"
    }
\]

Press F1 and search for keyboard ... [![](/assets/images/2017-10-05-22_18_55-tasks.json-database-Visual-Studio-Code.png)](http://bisaga.com/blog/wp-content/uploads/2017/08/2017-10-05-22_18_55-tasks.json-database-Visual-Studio-Code.png)The file is located in the Roaming folder in usual windows users location.

 

## Create attach debuger command

To intercept debug breakpoints in code you need to attach to running node process with a debugger session in the vscode editor. Create new "attach" launch configuration in .vscode/launch.json file:

...
 "configurations": \[
        {
            "type": "node",
            "request": "attach",
            "name": "Attach by Process ID",
            "processId": "${command:PickProcess}",
            "outFiles": \[
                "${workspaceRoot}/dist/\*\*/\*.js"
            \],
            "skipFiles": \[
                "node\_modules/\*\*/\*.js",
                "<node\_internals>/\*\*/\*.js"
            \]
        },        
...

Start debugging with "Attach by Process ID" configuration, then select the running **node server instance** . You can use **F5 key** when staying in the source file.

[![](/assets/images/2017-08-23-23_42_42-Greenshot-image-editor-300x73.png)](http://bisaga.com/blog/wp-content/uploads/2017/08/2017-08-23-23_42_42-Greenshot-image-editor.png)

Now execution should stop at defined breakpoints as expected.

[![](/assets/images/2017-08-23-23_44_34-Windows-Shell-Experience-Host-300x181.png)](http://bisaga.com/blog/wp-content/uploads/2017/08/2017-08-23-23_44_34-Windows-Shell-Experience-Host.png)

Debugging **doesn't stop** the tsc & nodemon processes ! In fact you are only attached to your running server process. If you detach from it, you will be able to work forward as you never dive into debugging.  If you change any typescript file and save the file, watchers will detect change and restart all processes. Though you will be automatically detached from the debugging session.

### Sample index.ts file

The file is a simplest REST service example in express/node program written in typescript, needed to test compiling, debugging environment.

import  \* as express from 'express';

const app: express.Express = express();

app.get('/', (req: express.Request, res: express.Response) => {

    let person = { name : 'Igor Babič' };

    person = Object.assign(person, req.query);

    res.json(person);
});

app.listen('3002',() => console.log('Server listening on port 3002'));

### Final version of package.json file:

{
  "name": "wodia",
  "version": "1.0.0",
  "description": "Work Diary",
  "main": "dist/index.js",
  "scripts": {
    "server": "tsc --watch | nodemon --inspect --watch dist",
    "test": "echo \\"Error: no test specified\\" && exit 1"
  },
  "author": "Igor Babic",
  "license": "MIT",
  "dependencies": {
    "express": "^4.15.4"
  },
  "devDependencies": {
    "@types/express": "^4.0.36",
    "@types/node": "^8.0.24",
    "nodemon": "^1.11.0",
    "typescript": "^2.5.1"
  }
}

Not sure if is important in this setup, but I use next workspace vscode settings:

{
    "search.exclude": {
        "\*\*/node\_modules": true,
        "\*\*/dist": true
    },
    "typescript.referencesCodeLens.enabled": true,
    "tslint.ignoreDefinitionFiles": false,
    "tslint.autoFixOnSave": true,
    "tslint.exclude": "\*\*/node\_modules/\*\*/\*" 
}

And the user settings for vscode :

// Place your settings in this file to overwrite the default settings
{
    "editor.fontFamily": "Consolas, 'Courier New', monospace",
    "editor.fontSize": 14,
    "window.zoomLevel": 1,
    "workbench.colorTheme": "Default Dark+",
    "terminal.integrated.shell.windows": "C:\\\\Programs\\\\PortableGit-2.12.0\\\\bin\\\\bash.exe",
    "terminal.integrated.shellArgs.windows": \["--init-file", "c:\\\\Users\\\\igorb\\\\.bash\_profile"\],
    "git.enableSmartCommit": true,
    "workbench.startupEditor": "newUntitledFile",
    "editor.renderWhitespace": "none",
    "window.restoreFullscreen": true,
    "window.restoreWindows": "all"
}

## Installed visual studio code extensions

[![](/assets/images/2017-10-05-21_50_34-database-Visual-Studio-Code-300x270.png)](http://bisaga.com/blog/wp-content/uploads/2017/08/2017-10-05-21_50_34-database-Visual-Studio-Code.png) 

## Setup GIT BASH as internal terminal inside code

Download and install [portable GIT Bash](https://github.com/git-for-windows/git/releases/) .

Create .bash\_profile in /Users/\_user\_name\_/ folder.

echo "RUN: .bash\_profile | GIT: SSH agent | PATH: ./node\_modules/.bin"
env=~/.ssh/agent.env

agent\_load\_env () { test -f "$env" && . "$env" >| /dev/null ; }

agent\_start () {
    (umask 077; ssh-agent >| "$env")
    . "$env" >| /dev/null ; }

agent\_load\_env

# agent\_run\_state: 0=agent running w/ key; 1=agent w/o key; 2= agent not running
agent\_run\_state=$(ssh-add -l >| /dev/null 2>&1; echo $?)

if \[ ! "$SSH\_AUTH\_SOCK" \] || \[ $agent\_run\_state = 2 \]; then
    agent\_start
    ssh-add
elif \[ "$SSH\_AUTH\_SOCK" \] && \[ $agent\_run\_state = 1 \]; then
    ssh-add
fi

unset env

export PATH="$PATH:./node\_modules/.bin"

 

**Add current project node modules binary folder to the path**

Add at least "./node\_modules/.bin/" folder to bash profile, to be able to call locally installed modules directly inside internal terminal window. The internal terminal window will open in the project root folder.

export PATH="$PATH:./node\_modules/.bin"

To be able to use git bash terminal inside visual studio code we add the terminal setup command in user settings:

"terminal.integrated.shell.windows": "C:\\\\Programs\\\\PortableGit-2.12.0\\\\bin\\\\bash.exe", 
"terminal.integrated.shellArgs.windows": \["--init-file", "c:\\\\Users\\\\igorb\\\\.bash\_profile"\],

Don't forget to define .bash\_profile file in the **shell arguments**.

After everything is properly set up you will be able to use internal projects node programs directly from the command line:

[![](/assets/images/2017-10-11-22_53_54-.bash_profile-database-Visual-Studio-Code.png)](http://bisaga.com/blog/wp-content/uploads/2017/08/2017-10-11-22_53_54-.bash_profile-database-Visual-Studio-Code.png)

How to setup git to run with ssh I described [here](http://bisaga.com/blog/programming/angular-and-git-project-setup/).
