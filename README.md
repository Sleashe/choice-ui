# Choice UI

This project provides a simple example of a web interface for Choice. please make sure to check 
[choice](https://github.com/Sleashe/choice) before.
Made with [VueJS](https://github.com/vuejs/vue)

![choice-screenshot](https://raw.githubusercontent.com/sleashe/choice-ui/master/public/screenshot.png)

Choice UI provides a simple web interface that connects to Choice API to help you monitor 
performances of your tests. The only available action for now is to elect or deny an option.
 
 ## Quick Start
 1. Clone this repo, navigate into it, and create a file called `.env.[development|production].local` 
 regarding your needs at the root of choice-ui folder. Edit this file and just add 
 `VUE_APP_CHOICE_URL=http://your-choice-api-url/choice` at the top of file.
 2. Use one of the following command provided by vue-cli-service:
 - npm run serve (development only)
 - npm run build (production) _Note: A dist folder will be created containing all app static 
 files. You will need to serve it using your own http server_

  ## Contribution
  **Your feedback is appreciated, and I love contributions!**
  I know the code is not perfect and feel free to comment, add features and refactor !
