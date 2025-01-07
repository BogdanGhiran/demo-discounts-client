# Introduction 
The is a Vue Js Project with a deployed at https://demo-discounts-client.gab16.com with a hub located at
It has capabilities for calling the "generate codes" and "use code" functionalities on the backend located at https://demo-discounts.gab16.com/ 
Created using the vite vue template(command )

npm create vite@latest my-vue-app -- --template vue


# Getting Started

1.	Installation process - open a terminal at the repo location, run npm install, run npm run dev for the dev build or npm run build for the prod build
2.	Software dependencies - make sure to have npm installed

# Build and Test
Open the project in Visual Studio Code and run the "npm run dev" command. 
By default, it tries to create the signalR connection using the string located in the .env file when doing so, and the .env.production file contains the url for the prod app

#Usage 
##The interface is split in half: 
###On the left side, an "Admin" mock, that has two buttons:

Clicking Generate Codes generates a few codes based on the length and count parameters

Clicking Generate & Display Codes replies with the generated codes. This was not asked as part of requirements, and is an extra endpoint to use for debugging/testing.
The Generate & Display button will also generate buttons for quick usage of codes

###The right side displays a "Client" mock has a code input:
It was built to mock a kind of a shopping cart. 
Under the "items" list there is an input for the discount code that is submitted via the Use Code button

There's not much logic behind it, but the total cost is lowered if the code used last has worked, and it stays at 31.5 otherwise.


# Deployment
Every time the main branch receives an update(commit) the [Azure Pipeline](https://dev.azure.com/demo-org-bg/demo-discounts/_build?definitionId=2) will pick up the changes, rebuild the service, and if that is successful, copy it over to github, where a cloudflare page will deploy the new service version at [demo-discounts-client.gab16.com](https://demo-discounts-client.gab16.com/status)