Set Working Directory

```bash
cd /Volumes/LocalDrive/Docker
```

Let's create a directory for our application

```bash
mkdir Docker
```

Lets Create Data loading script and place it in a folder

```bash
mkdir data-prep

touch data-prep/data-prep-script.R
```

Place the data inside the data-prep folder

Now Create APP.R file to build our application

```bash
mkdir shiny-app

touch shiny-app/app.R
```

After completing the app, place the saved RDS data file in this directory

Now Let's create RProj file for the application with default parameters

```bash
touch shiny-app-docker.Rproj
```

Time to create Dockerfile to build our docker

```bash
touch Dockerfile
```

Lets build our Docker Image

```bash
docker build -t my-shiny-app .
```

Run the docker 

```bash
docker run --rm -p 3838:3838 my-shiny-app
```

Check the app running in docker

```bash
http://localhost:3838
```

Lets push our docker image to Azure

Login into Azure

```bash
az login
```

Create Azure Resource group and provide region

```bash
az group create --name shinyapps --location northeurope
```

Create Azure Container Service to host our Docker Image

```bash
az acr create -n shinyappdocker -g shinyapps --sku Basic
```

Create A Service plan for our application, choosing Linux Basic Free version

```bash
az appservice plan create -g shinyapps -n shinyappplan --sku FREE --is-linux
```

One more step to push docker to Azure

Log into Azure container registry

```bash
docker login shinyappdocker.azurecr.io
```

It will prompt for Username and Password

Log into Azure Portal in a browser, go to the resource group we created **shinyapps** ---> **shinyappdocker** ---> **Access Keys** --> Enable Admin User ---> copy Username and password 

check for login succesful message

Tag the image of the docker to the Azure container registry

```bash
docker tag my-shiny-app shinyappdocker.azurecr.io/my-shiny-app
```

Push the docker image to the cloud

```bash
docker push shinyappdocker.azurecr.io/my-shiny-app
```

Time to deploy

```bash
az webapp create -g shinyapps -p shinyappplan -n myshinyappindocker -i shinyappdocker.azurecr.io/my-shiny-app
```

Once its deployed, get the link for the application

```bash
https://myshinyappindocker.azurewebsites.net
```


