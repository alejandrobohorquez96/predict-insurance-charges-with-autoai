# Cree una aplicación para predecir el coste de la prima de su seguro con AutoAI 

## Inicie Sesión/Registrese a IBM Cloud:  http://ibm.biz/insuranceChargesUsingAutoAI 

forked from IBM/predict-insurance-charges-with-autoai
![finalDemo](https://user-images.githubusercontent.com/10428517/82013347-f7e71500-962e-11ea-9c28-2dec7d5b30cd.gif)


Como se ha mostrado anteriormente, esta aplicación aprovecha los modelos de AutoAI para predecir los gastos de su seguro y ayuda al usuario a entender la repercusión de fumar o tener un índice de masa corporal (IMC) elevado en el valor de la prima de su seguro.

El valor de las primas de seguros en todo el mundo sigue disparándose por encima de los 5 billones de dólares, aún así se sabe que la mayoría de estos costes son evitables. Por ejemplo, el simple hecho de dejar de fumar y reducir su IMC en unos pocos puntos podría suponer una reducción de miles de dólares en sus primas. En esta aplicación, estudiamos los efectos de la edad, el tabaquismo, el IMC, el sexo y la región para determinar la diferencia que estos factores pueden suponer en el coste de su prima. 

Al utilizar nuestra aplicación, los clientes ven la diferencia radical que suponen sus elecciones de estilo de vida en sus  en sus primas de seguro. Aprovechando la IA y el concepto de AutoAI, ayudamos a los clientes a comprender en qué medida el tabaquismo aumenta su prima, prediciendo cuánto tendrán que pagar en cuestión de segundos.


## Descripción

Con IBM AutoAI, se automatizan todas las tareas relacionadas con la creación de modelos predictivos. Verá cómo AutoAI genera rápidamente modelos que ahorran tiempo y esfuerzo y ayudan a acelerar el proceso de toma de decisiones en el ciclo de vida de un proyecto de MachineLearning. Usted creará un modelo que a partir de un conjunto de datos que incluyen la edad, el sexo, el IMC, el número de hijos, las preferencias de fumar, la región y el coste de la prima predecirá el coste de la prima del seguro médico que paga un individuo.

Cuando haya completado este tutorial, entenderá cómo:

* Configurar, rápidamente, los servicios en IBM Cloud para construir el modelo.
* Ingerir los datos e iniciar el proceso de AutoAI.
* Construir diferentes modelos utilizando AutoAI y evaluar el rendimiento.
* Elegir el mejor modelo y completar el despliegue.
* Generar predicciones usando el modelo desplegado haciendo llamadas REST.
* Comparar el proceso de usar AutoAI y construir el modelo manualmente.
* Visualizar el modelo desplegado utilizando una aplicación front-end.


### Componentes de la arquitectura

![Architecture Components](https://media.github.ibm.com/user/21063/files/3b77e580-913c-11ea-9dea-425b1d4f4ee0)

## Descripción del flujo
1. El usuario crea un servicio de IBM Watson Studio en IBM Cloud.
2. El usuario crea un servicio de almacenamiento de objetos en IBM Cloud y lo añade a Watson Studio.
3. El usuario carga el archivo de datos de primas de seguros en Watson Studio.
4. El usuario crea un Experimento AutoAI para predecir la prima del seguro en Watson Studio
5. AutoAI utiliza Watson Machine Learning para crear varios modelos, y el usuario despliega el modelo de mejor rendimiento.
6. El usuario utiliza la aplicación web Flask para conectarse al modelo desplegado y predecir el cobro del seguro.



## Prerequisitos

Este patrón de Cloud asume que usted tiene una cuenta de **IBM Cloud**. Vaya al siguiente enlace para registrarse en una cuenta de prueba sin coste alguno, sin necesidad de tarjeta de crédito. 
  - [Cuenta IBM Cloud](https://ibm.biz/AutoAI-insurance)
  - [Python 3.8.2](https://www.python.org/downloads/release/python-382/)
  

## Paso 0. Descargue el conjunto de datos  
Utilizaremos un conjunto de datos de seguros de Kaggle. Puedes encontrarlo en (https://www.kaggle.com/noordeen/insurance-premium-prediction). Haga clic en el botón `Download`, y debería ver que descargará un archivo llamado `insurance-premium-prediction.zip`. Una vez descomprimido el archivo, debería ver `insurance.csv`.
Este es el conjunto de datos que utilizaremos para el resto del ejemplo. Recuerde que este ejemplo es puramente didáctico y que puede utilizar cualquier conjunto de datos que desee. Puede utilizar cualquier conjunto de datos que desee; nosotros simplemente hemos elegido este.


## Paso 1. Clone el repositorio de código
Clone este repositorio de código en su computador en el destino de su elección:
```
git clone https://github.com/alejandrobohorquez96/predict-insurance-charges-with-autoai.git
```
This gives you access to the notebooks in the `notebooks` directory. To explore the data before creating a model, 
you can look at the [Claim Amount Exploratory](https://github.com/IBM/predict-insurance-charges-with-ai/blob/master/notebooks/Claim%20Amount%20Exploratory.ipynb) notebook, and create a [IBM Cloud Object Storage](https://cloud.ibm.com/catalog/services/cloud-object-storage) service, and paste your credentials in the notebook to run it. This step is purely optional.

## Step 2. Explore the data (optional)

#### If you want to run the notebook that is explored below, go to [`notebooks/Claim Amount Exploratory.ipynb`](https://github.com/IBM/predict-insurance-charges-with-ai/blob/master/notebooks/Claim%20Amount%20Exploratory.ipynb).
* Within Watson Studio, you explore the data before you create any 
machine learning models. You want to understand the data, and find any trends between 
what you are trying to predict (insurance premiums <b>charges</b>) and the data's features.

* Once you import, you see the data into a data frame, and call the 
`df_claim.head()` function, you see the first 5 rows of the data set. 
You see the features to be `age`, `sex`, `bmi`, `children`, `smoker`,
and `region`.

![scatter](https://media.github.ibm.com/user/79254/files/ed325a80-8a48-11ea-8fcf-d1e9877458ef)

* To check if there is a strong relationship between `bmi` and `charges` you 
create a scatter plot using the seaborn and matplotlib libraries. You 
see that there is no strong correlation between `bmi` and `charges`,
as shown below.

![scatter](https://media.github.ibm.com/user/79254/files/2965bb00-8a49-11ea-81f9-a528fc1e2606)

* To check if there is a strong relationship between `sex` and `charges` you create a box plot. You see that the average claims for males and females are similar, whereas males have a bigger proportion of the higher claims.

![scatter](https://media.github.ibm.com/user/79254/files/32ef2300-8a49-11ea-93aa-990f85eccf9d)

* To check if there is a strong relationship between being a `smoker` and `charges` you create a box plot. You see that if you are a smoker, your claims are much higher on average.

![scatter](https://media.github.ibm.com/user/79254/files/4221a100-8a48-11ea-8104-64f50d8ae92f)

* Let's see if the `smoker` group is well represented. As you see, below, it is. 
There are around 300 smokers, and around 1000 non-smokers.

![scatter](https://media.github.ibm.com/user/79254/files/477eeb80-8a48-11ea-83a0-9a073bf4f176)

* To check if there is a strong relationship between being a `age` and `charges` you create a scatter plot. You see that claim amounts increase with age, and tend to form groups around 12,000, 30,000, and 40,000.

![scatter](https://media.github.ibm.com/user/79254/files/5bc2e880-8a48-11ea-8dad-8effab71a8ac)

If you want to see all of the code, and run the notebook yourself, check the data folder above.

## Step 3. Create IBM Cloud services

![Creating-Watson-Service](https://user-images.githubusercontent.com/54094367/111079988-d50ae300-8515-11eb-8616-bec6485c140c.gif)

1.	Login to your IBM Cloud account: http://ibm.biz/insuranceChargesUsingAutoAI 

2.	Within your IBM Cloud account, click on the top search bar to search for cloud services and offerings. Type in “Watson Studio” and then click on Watson Studio under “Catalog Results”.

3.	This takes you to the Watson Studio service page. Select a region, “Lite” plan (Free) and give your service a unique name. Click on “Create” and this creates a Watson Studio instance for you. 

4.	 Once the service instance is ready, you will be redirected to the Watson Studio page. Click on the “Get Started” button to launch Watson Studio in a new tab. This might take few minutes to set up the service. 

![Creating-empty-project](https://user-images.githubusercontent.com/54094367/111079989-d5a37980-8515-11eb-9cd0-cea012081dd3.gif)

5.	Under the heading “work with data” you will find a link that says, “Create a project”. Click on “Create a project”. Next, click on “Create an empty project”.

6.	On the new project page, give your project a name. You will also need to associate an IBM Cloud Object Storage instance to store the data set.

7.	Under “Select Storage Service”, click on the “Add” button. This takes you to the IBM Cloud Object Store service page. Leave the service on the “Lite” tier and then click the “Create” button at the bottom of the page. You are prompted to name the service and choose the resource group. Once you give a name, click “Create”.

8.	Once the instance is created, you’re taken back to the project page. Click on “refresh” and you should see your newly created Cloud Object Storage instance under Storage.

9.	Click the “Create” button at the bottom right of the page to create your project.

![Add-dataset](https://user-images.githubusercontent.com/54094367/111079986-d4724c80-8515-11eb-8d55-89185c9add23.gif)

10.	Click on the “Add to project” button on the top right corner. From the pop-up window select “Data”.

11.	In the column, on the right, click on “browse”. Navigate to the folder where you downloaded the data set to and select “insurance.csv”

12.	Watson Studio takes a couple of seconds to load the data, and then you should see the import has completed. To make sure it has worked properly, you can click on “Assets” on the top of the page, and you should see your insurance file under “Data Assets”.


## Step 4. Create and Run AutoAI experiment

![create-AutoAI-project](https://user-images.githubusercontent.com/54094367/111079982-d2a88900-8515-11eb-8b58-910c39785d27.gif)

1.	Once you have added the data set, click on the “Add to project” button on the top right corner. This time select “AutoAI experiment”.

2.	On the New AutoAI Experiment page, give a name to your project. 

3.	Next, you need to add a Watson Machine Learning instance. On the right side of the screen, click on “Associate a Machine Learning service instance”.

4.	On the Associate service page, click on the “New Service” button on the right side of the screen. From the pop-up screen, select “Machine Learning”.

5.	Select the appropriate region. It is recommended to build your machine learning service instance in the same region that you created your Watson Studio service. Select the “Lite” (free) plan. Give your instance a unique name. Click on “Create”.

6.	Once you create your machine learning service, on the next page, check the box with your machine learning service instance. Next, click on “associate service” on the right corner. 

7.	Once the service is successfully associated, you will be redirected to new AutoAI experiment page. Click on “Reload” on the right side of the screen. You should see your newly created machine learning instance. Click on “Create” on the bottom right part of your screen to create your first AutoAI experiment!

![settings-of-project](https://user-images.githubusercontent.com/54094367/111079981-d20ff280-8515-11eb-914f-7c4eddaeadcb.gif)

8.	After you create your experiment, you are taken to a page to add a data source to your project. Click on “Select from project” and then add the insurance.csv file. Click on Select asset to confirm your data source.

9.	Next, you see that AutoAI processes your data, and you see a What do you want to predict section. Select the expenses as the Prediction column.

10.	Next, let's explore the AutoAI settings to see what you can customize when running your experiment. Click on Experiment settings.First, you see the data source tab, which lets you omit certain columns from your experiment. You choose to leave all columns. You can also select the training data split. It defaults to 85% training data. The data source tab also shows which metric you optimize for. For the regression, it is RMSE (Root Mean Squared Error), and for other types of experiments, such as Binary Classification, AutoAI defaults to Accuracy. Either way, you can change the metric from this tab depending on your use case.

11.	Click on the Prediction tab from within the Experiment settings. There you can select from Binary Classification, Regression, and Multiclass Classification.

12.	Lastly, you can see the Runtime tab from the Experiment settings this shows you other experiment details you may want to change depending on your use case.

13.	Once you are happy with your settings, ensure you are predicting for the expense’s column, and click on the run Run Experiment button on the bottom-right corner of the screen.

14.	Next, your AutoAI experiment runs on its own. You see a progress map on the right side of the screen which shows which stage of the experiment is running. This may be Hyper Parameter Optimization, feature engineering, or some other stage.

15.	You have different pipelines that are created, and you see the rankings of each model. Each model is ranked based on the metric that you selected. In the specific case that is the RMSE(Root mean squared error). Given that you want that number to be as small as possible, you can see that in the experiment, the model with the smallest RMSE is at the top of the leaderboard.

![completed-pipeline](https://user-images.githubusercontent.com/54094367/111080010-e8b64980-8515-11eb-8feb-e464930ed949.png)

16.	Once the experiment is done, you see Experiment completed under the Progress map on the right-hand side of the screen.

![best-pipeline](https://user-images.githubusercontent.com/54094367/111080015-ebb13a00-8515-11eb-8cb2-b06d9b10c7dc.png)

17.	Now that AutoAI has successfully generated eight different models, you can rank the models by different metrics, by clicking on the drop-down next to “Rank by:” on the top right corner of the screen, such as explained variance, root mean squared error, R-Squared, and mean absolute error. Each time you select a different metric, the models are re-ranked by that metric.

![best-pipeline-features](https://user-images.githubusercontent.com/54094367/111079979-d1775c00-8515-11eb-97d3-7dfba7d85ff4.gif)

18.	In our case, we have RMSE as the experiment's metric. You see the smallest RMSE value is 4444.108, from Pipeline 4. Click on “Pipeline 4”.

19.	On the left-hand side, you can see different “Model Evaluation Measures”. For this particular model, you can view the metrics, such as explained variance, RMSE, and other metrics.

20.	On the left-hand side, you can also see “Feature Transformations”, and “Feature Importance”.

21.	On the left-hand side, click on “Feature Importance”. You can see here that the most important predictor of the insurance premium is whether you are a “smoker” or a “non-smoker”. This is by far the most important feature, with “bmi” coming in as the second most important. This makes sense, given that many companies offer discounts for employees who do not smoke.

## Step 5. Create a deployment and test your model

![deployment-space](https://user-images.githubusercontent.com/54094367/111079977-cfad9880-8515-11eb-8a80-2e37cdc4b678.gif)

1.	Once you are ready to deploy one of the models, click on “Save As” at the top-right corner of the model you want to deploy. Save it as a “Model” and name your model as you want. Click on “Create” 
Note: We show you how to save it as a notebook in step 6.

2.	Once the model is successfully saved, click on the “View in project” in the green notification on the right side of the screen. Alternatively, you can also find your model saved in the “Assets” tab under “Models”.

3.	Next, you are taken to a screen that has the overview of the model you just saved. Click on “Promote to deployment space” on the top right corner of your screen.  Alternatively, if you’re doing it from the Assets tab, then under the “Models” section, click on the 3 dots on the right side of your screen and click “promote”.

4.	On the Promote to space page, you need a target space to promote your model. Click on “New space +” on the right side of your screen. 

5.	Next, on the Create a deployment space screen, give your space a name, make sure the right cloud object storage is selected, and select your machine learning service instance. For this experiment, selecting the machine learning service is mandatory as we need to build a prediction model. Then click on “Create”.

6.	Once the space is ready, click on “Close” in the pop-up and you will be redirected to the promote to space page. You see your newly created space under the “Target space”. Once you’re happy with your selections, click on “Promote”. 

![deploy](https://user-images.githubusercontent.com/54094367/111079975-cf150200-8515-11eb-8dc0-89b06f4b6aa5.gif)

7.	Once the model is successfully promoted, you will see a green notification box, click on “deployment space” in the notification. Alternatively, you can also find your deployment spaces when you click on the hamburger sign on the top left most side on your screen. 

8.	You will be redirected to the deployments page, where you will find your promoted model. Hover over the row, to see a rocket shaped icon, click on the icon to deploy you model. 

9.	In the dialog box, select “Online” as your deployment type, give your deployment a name and click “Create”.

10.	Click on the “Deployments” tab to see the status of your deployment. 

![deployment-completed](https://user-images.githubusercontent.com/54094367/111080017-ece26700-8515-11eb-8b6d-7bb927b6bce8.png)

![test](https://user-images.githubusercontent.com/54094367/111079973-cd4b3e80-8515-11eb-9446-5d52a90bbf0e.gif)

11.	Once the deployment is completed, click on the name your deployment. 

12.	On this page you find the API references, endpoint and code snippets to help you integrate your model with your applications. 

13.	To test your model, click on the “Test” tab. You can select a row from the data set and enter the data in the fields. Enter the age, sex, bmi, children, smoker and region and then click on the “Predict” button at the bottom. 

14.	To validate the prediction, you check the data file that you used to train the model. As you can see, the model predicted a premium of 18524.04, when you enter age 19, bmi: 27.9, children: 0, smoker: yes, region: southwest. This is relatively close to the model's prediction, so we know the model is working properly.




## Step 6. Run the application
The driver code to run the application can be found under the web-app folder within the git repository that was cloned from [Step 1](#step-1-clone-the-repo). To run and test your deployed model through this Python-based user-interface,
you need to replace the following information within `web-app/app.py`: 

1) Your Watson Machine Learning (which is associated with this deployed model) `Instance ID` and `apikey`.
1) Your deployed model's deployment URL, so you can make a POST request.
1) Your IBM Cloud IAM token, to authorize yourself. 

Now, you go into detail on how to gather these credentials. If you already know how to do this, you can
skip the steps below, and  go straight to running the application.


### 6.1 Get IBM Cloud API key

<!-- ![apikey-instanceID](https://media.github.ibm.com/user/79254/files/4119b680-8e30-11ea-8bc3-97ab1558fc23) -->
* Generate an IBM Cloud apikey by going to `cloud.ibm.com` and then from the top-right part of the screen click on `Manage`-> `IAM`.

![manage](https://user-images.githubusercontent.com/10428517/95252784-4d84af80-07d2-11eb-9fdd-1fe119329cef.png)


* Next, click on `API keys` from the left side-bar. Next click on `Create an IBM Cloud API key`.

![create-api-key](https://user-images.githubusercontent.com/10428517/95252429-d4855800-07d1-11eb-80e3-fd3b55d5d0a8.png)


* Name the key as you wish, and then click `Create`. 

![create](https://user-images.githubusercontent.com/10428517/95252417-d222fe00-07d1-11eb-95a9-8bd7c9d4bbce.png)


* Once the key is created, click on the `Download` button.

![download](https://user-images.githubusercontent.com/10428517/95252393-ccc5b380-07d1-11eb-8d14-9d7154f71b86.png)

### 6.2 Get model deployment ID

<!-- ![model-deploy-url](https://user-images.githubusercontent.com/10428517/81858555-caa84300-9518-11ea-9088-3f088216da83.gif) -->

* From inside Watson Studio (Or Cloud Pak for Data), click on `Deployment Spaces`. 

* From there, click on the name of the deployment in which you deployed your model to.

* Next, click on on the name of the model.

* Next, click on the deployment of the model.

* From there, you will be taken to the deployment API reference page - on the right hand side you can see the `Deployment ID`. Go ahead and copy that 
and keep it handy - you will need to paste that into your `app.py` page.

![deploy-id](https://user-images.githubusercontent.com/10428517/95250925-a737aa80-07cf-11eb-9ff2-a51399f7c300.png)


### 6.3 Generate the access token

* From the command line, type ```curl -V``` to verify if cURL is installed in your system. If cURL is not installed, refer to [this](https://develop.zendesk.com/hc/en-us/articles/360001068567-Installing-and-using-cURL#install) instructions to get it installed.

* Execute the following cURL command to generate your access token, but replace the apikey with the 
apikey you got from [step 7.1](https://github.com/IBM/predict-insurance-charges-with-autoai#71-get-IBM-Cloud-API-key) above. 

For Linux Mac
```
curl -X POST 'https://iam.cloud.ibm.com/oidc/token' -H 'Content-Type: application/x-www-form-urlencoded' -d 'grant_type=urn:ibm:params:oauth:grant-type:apikey&apikey=<api-key-goes-here>'
```
For Windows (If you are receiving a "Protocol "https" not supportted or disabled in libcurl replace ' ' for " ")
```
curl -X POST "https://iam.cloud.ibm.com/oidc/token" -H "Content-Type: application/x-www-form-urlencoded" -d "grant_type=urn:ibm:params:oauth:grant-type:apikey&apikey=<api-key-goes-here>"
```

As shown in the image below, the apikey can be copy and pasted from the downloaded file from the end of [step 7.1](https://github.com/IBM/predict-insurance-charges-with-autoai#71-get-IBM-Cloud-API-key). The curl request would look something like this after the apikey is pasted in:

![api](https://user-images.githubusercontent.com/10428517/95252350-c0d9f180-07d1-11eb-841e-d5cd72da72d4.png)

```
curl -X POST 'https://iam.cloud.ibm.com/oidc/token' -H 'Content-Type: application/x-www-form-urlencoded' -d 'grant_type=urn:ibm:params:oauth:grant-type:apikey&apikey=aSULp7nFTJl-jGx*******aQXfA6dxMlpuQ9QsOW'
```


### 6.3 (Windows Users only) - Using Windows 10 and Powershell to generate the access token

* Install python.org Windows distro 3.8.3 from http://python.org - make sure to add the /python38/scripts folder path to the $PATH environment, if you do not, you will get errors trying to run flask (flask.exe is installed to the scripts folder)

* Remove powershell alias for curl and install curl from python3.8

```
PS C:/> remove-item alias:curl

PS C:/> pip3 install curl
```

* 3.	Execute curl to get secure token from IBM IAM. Please note that the token expires after 60 minutes. If you get an internal server error from the main query page (The server encountered an internal error and was unable to complete your request. Either the server is overloaded or there is an error in the application), it may be due to the token expiring. Also note that in powershell the continuation character is ‘

```
curl -X POST 'https://iam.cloud.ibm.com/oidc/token' -H 'Content-Type: application/x-www-form-urlencoded' -d 'grant_type=urn:ibm:params:oauth:grant-type:apikey&apikey=<apikey>'
```

### 6.4 Modify the 'web-app/app.py' file

* Copy and paste the access token into the header in the `web-app/app.py` file. Replace the line
`" TODO: ADD YOUR IAM ACCESS TOKEN FROM IBM CLOUD HERE"` with your token.

<!-- ![watsonML](https://user-images.githubusercontent.com/10428517/81858562-cc720680-9518-11ea-953b-f96aab8fcc2f.gif) -->

* Modify the `app.py` file within the `web-app` directory to change the POST request with your deployment ID. The finished line should look like the following:

```
response_scoring = requests.post("https://us-south.ml.cloud.ibm.com/ml/v4/deployments/18c7f626-04d2-4d1e-9b9b-bf2e6/predictions?version=2020-09-01", json=payload_scoring, headers=header)
```

* Once you've updated the token and the deployment id, your code should look similar to this. If it does, save it!

<img width="1420" alt="code" src="https://user-images.githubusercontent.com/10428517/95253984-13b4a880-07d4-11eb-825b-d890481840bf.png">


* Great job! You are ready to run the application! 

### 6.5 Install dependencies, and run the app

Note, this app is tested on this version of Python 3.8.2

Within the `web-app` directory, run the following command: 

```
pip3 install flask flask-wtf urllib3 requests
```

![finalDemo](https://user-images.githubusercontent.com/10428517/82013347-f7e71500-962e-11ea-9c28-2dec7d5b30cd.gif)

Next, run the following command to start the flask application.

```
flask run
```

### 6.5 (Windows Users only) - Running the app using Windows 10 and Powershell

* Install flask and dependencies

```
PS C:/> pip3 install flask flask-wtf urllib3 requests

Verify modules have been installed in the 'python38/scripts' folder
```

* Run 'web-ap/app.py' from the local directory using flask

```
PS C:/> set FLASK_APP=app.py

PS C:/> flask run
```

### 6.6 Run application from browser

* Go to `127.0.0.1:5000` in your browser to view the application. Go ahead and fill in the form, and click on the `Predict`
button to see your predicted charges based on your data. 

* As is expected, if you are a smoker, this drastically increase the insurance charges. 

## Bonus Section - Visualize the data and share your findings via Cognos Dashboard Embedded.
* You can add a Dashboard which is a lean version of Cognos Dashboard available on IBM cloud from "Add to Project" option in your Watson Studio project.

* You can start finding patterns in your data by easily visualizing various data points. This can get your exploration started within few minutes and with no coding involved
![Cognos-1](https://media.github.ibm.com/user/34798/files/46c6ca80-8e37-11ea-9974-d76d2cc2db87)

* From visualizing this data you can see the relation in the data points, how Gender, BMI, # of children  and smoking might influence the insurance premium.

* Dashboards are very interactive and makes it easy to play with data.
![Cognos-2](https://user-images.githubusercontent.com/10428517/81858754-0f33de80-9519-11ea-8fea-807d17280d9a.gif)

* You can also pivot and summarize your measures to quickly look at all your measures
![Cognos-3](https://user-images.githubusercontent.com/10428517/81858750-0e02b180-9519-11ea-9018-4258c3c10704.gif)

* Stop working in Silos and share your findings with your team in two clicks.
![Cognos-3](https://media.github.ibm.com/user/34798/files/8ab9cf80-8e37-11ea-9554-a85f6ad6186f)


## Related Links
* [Fraud Prediction Using AutoAI](https://github.com/IBM/predict-fraud-using-auto-ai)
* [Use AutoAI to predict Customer Churn tutorial](https://developer.ibm.com/tutorials/watson-studio-auto-ai/)
* [Predict Loan Default with AutoAI tutorial](https://developer.ibm.com/tutorials/generate-machine-learning-model-pipelines-to-choose-the-best-model-for-your-problem-autoai/)

## License
This code pattern is licensed under the Apache Software License, Version 2. Separate third-party code objects invoked within this code pattern are licensed by their respective providers pursuant to their own separate licenses. Contributions are subject to the [Developer Certificate of Origin, Version 1.1 (DCO)](https://developercertificate.org/) and the [Apache Software License, Version 2](https://www.apache.org/licenses/LICENSE-2.0.txt).

[Apache Software License (ASL) FAQ](https://www.apache.org/foundation/license-faq.html#WhatDoesItMEAN)

