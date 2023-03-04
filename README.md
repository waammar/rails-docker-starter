# rails-docker-starter
A Docker compose bootstrap layout for your Ruby On Rails project

This is a highly opinionated pattern of how to use docker, docker-compose, Nginx with RoR. A vision of how an environment of a web application (aka Rails) can be organized in container ways and can be easily reproductible.

## Requirements
* [git](https://git-scm.com/)
* [make (3.81 or later)](https://savannah.gnu.org/projects/make/)
* [docker (19.03.12 or later)](https://docs.docker.com/engine/install/)
* [docker-compose (1.26.0 or later)](https://docs.docker.com/compose/install/)

# Install

### 1. Clone the repo
```bash
git clone git@github.com:waammar/rails-docker-starter.git
```
### 2. Remove .git directory
This step will remove the environment repository and allows you to create your own.
```bash
cd rails-docker-starter && rm -rf .git
```

# Usage
Here is the very basic usage of the current repository not really as a pattern, but as a tool for start develop a new Ruby On Rails application.

FROM THE SCRATCH.

Probably, you have cloned this repo already, so we can start using it. But as long as you just cloned it and have no application, let's create one.

`cd ${PROJECT_PATH}/environment`

"environment" - is a directory that holds all of the magic which makes our environment available for our incredible new project. If you are there, that suggests that you are going to interact not really with a project itself, but with an environment. It's like a shell for your project, it protects your app from foreign invasions and holds 100% compatible libraries, operating systems, tools, etc.


Everything starts with editing .env.example. This file contains several initial variables for the new projects, but now we need just PROJECT_NAME.<br>
Change PROJECT_NAME variable to your project name.<br> 
<font color="red">Attention here!</font><br>
PROJECT_NAME should be in snake case with underscores, no spaces, no special chars, and no EOL symbols. Just make it nice_and_clean.<br>

Once the project name was given we can initialize our app.

`make init`

This command will create your application folder, and prepare your environment to be built.
The next things you have to do is build your containers, run the environment, and run an application server.

`make build`

With this command, your containers will be prepared for running and all packages from rails Gemfile will be installed inside containers.
Execution of this command is obligatory if you changed your Gemfile.

`make run`

With this command, your environment would run, then you can interact with it, but the final step is still forward.
To be able to use ["rails console"](https://guides.rubyonrails.org/getting_started.html#using-a-model-to-interact-with-the-database) and start ["rails server"](https://guides.rubyonrails.org/getting_started.html#starting-up-the-web-server) you have to enter into the environment shell. 

`make shell`

Finally, you can start rails server just as ROR [guides](https://guides.rubyonrails.org/getting_started.html#starting-up-the-web-server) 
told us to do. But here is the thing, as far as our app running into the docker container we **must** use `--binding=0.0.0.0`, also our nginx server by default listening 3000 port for rails, so we **must** specify a port with `-p 3000` argument.

`bin/rails s -p 3000 --binding=0.0.0.0`

Once you did it your project will be available by this URL http://${PROJECT_NAME}.localhost. By default, it's http://test_project.localhost.


# Environment stages
In the ./environment directory you can find `docker-compose.development.yml` file. If you need some other environment you are able to do so with next steps:
* Create `docker-compose.${env_name}.yml` and configure it by your project needs. 
* In ./environment/containers you'll find three initial containers. In those directories you will find Dockerfile files with described stages. Create your own stage.
* Build your stage 
  ```bash
  make build t=${env_name}
  ```
* Run your stage
  ```bash
  make start t=${env_name}
  ```
* Open console in your stage
  ```bash
  make shell t=${env_name} c=${container_name}
  ```  
  ```bash
  #For example, when you are running the following command
  make shell
  #it's the same as
  make shell t=development c=ruby
  ```

# Tooling
## VS Code
Install the extensions
* [Ruby Debug](https://marketplace.visualstudio.com/items?itemName=castwide.ruby-debug)
* [Tasks](https://marketplace.visualstudio.com/items?itemName=actboy168.tasks)

Then you should be able to see a list of prepared tasks for creating and running new ROR application


Here is the meaning of the tasks:
* `Init new project` - use this task just once to initialize a new project.
* `Build environment` - use it when you need to update the environment or you commit to Gemfile.
* `Run environment` - this task runs the environment. Also it starts a debugger session and waits for connection. More info about debugger [here](https://github.com/rubyide/vscode-ruby/wiki/3.-Attaching-to-a-debugger) 
* `Run shell ruby` - this task "teleports" you to the command shell where you can execute **rails**, **rake**, **bundle**, etc. commands.
* `Stop environment` - this task stops the environment.

By default, your app will be initialized in ./application directory. But you can change it for any other you like in the .env.example file which is in the ./environment directory.

You can change very basic environment variables for your application with .env.example file. Like so: 
* PROJECT_NAME
* APP_PATH
* DB_PORT
* DB_NAME
* DB_USER
* DB_PASSWORD

Basically this is all you need to start your new cool project. Using Visual Studio Code.

After your VSCode has been connected to the debugger session your project will be able to visit by `http://${PROJECT_NAME}.localhost`. By default, it's `http://test_project.localhost`.
