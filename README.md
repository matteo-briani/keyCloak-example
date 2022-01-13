# keyCloak-example

Heavily based on the [Keycloak-Identity-and-Access-Management-for-Modern-Applications](https://github.com/PacktPublishing/Keycloak-Identity-and-Access-Management-for-Modern-Applications) book.

## Setup the project

Start KeyCloak and Postgres from docker-compose
```
docker-compose up -d keycloak postgres
```
Backend and Frontend have to be ran from the respective directories using:
```
npm install
npm start
```
If the KeyCloak server is not running on localhost, set the ```KC_URL``` env var for the frontend.
In this case you also need to specify the ```SERVICE_URL``` env var of the backend (instead of ```http://localhost:3000/secured```, ```http://<IP_ADDR>:3000/secured```).

### Docker-compose only !!Not yet ready!!
Ignore this, a redirection problem occur when starting the backend and the frontend using ```docker-compose``` directly due to the internal docker-compose network.
Use the previous instruction for the backned and the frontend services.

## Setup KeyCloak

Navigate to ```localhost:8080```, which is the KeyCloak service.
Enter in KeyCloak using the admin credentials that can be found in the ```docker-compose.yaml```, under the KeyCloak service.

### Realm

Create a realm call ```myrealm```. 
It is important to use the exact name provided in this guide. 
Once you understand the meaning of each variable you are free to change them and adjust the project accordingly.

### Role

In the ```Role``` section add a role.
Name the created role ```myrole``` and feel free to give it a general description.
When you will develop your own application, you will likely create roles such as `admin`, `user`, `manager`, etc.

### Create a group 

Create a group in the ```Group``` section called ```mygroup```.
In your application this might be a project name or a section of your business.

### Create a user (or more)

Enter in the ```Users``` section and create a new user.
Add ```username```, ```email``` (fake one, doesn't matter now), ```First name```, ```Last name```.
Click on the save button.

Now, you should be prompted to the created user admin panel, where you can manage ```Details```, ```Attributes```, ```Credentials```, ```Role mappings```, ```Groups```, ```Consents```, ```Sessions```.
Add the group ```mygroup``` from the section ```Group``` and the role ```myrole``` from the section ```Role mapping```.

Setup a password for the created user in the section ```Credentials```.
Just enter the password twice and turn off the ```temporary``` button.

Great, we have a user beloging the the group ```mygroup``` and having a role ```myrole```.

### Add a client 

Clients are the service that use the auth capabilities of KeyCloak.
Up to now, KeyCloak does not know about our frontend and backend application.
In fact, if you navigate to ```localhost:8000``` (<- the frontend) and click the ```login``` button, you will be prompt to KeyCloak that will kindly reply that it does not know the client.

So, in the ```Client``` section add a client named ```myclient``` and enter ```http://localhost:8000``` as ```root url```. Client protocol should be already set as ```openid-connect```.
Now, KeyCloak knows the client!
For your knowledge, take a look at the default value of this created client. You will see a ```valid redirect URi``` already set as ```http://localhost:8000```. 
A lot of other configuration options are available here. For the time being, we are good to go with the default ones.

## Test the application

First, without being logged in, try to query the secured backend API (if you don't have ```curl``` you can just browse the location).
```
curl localhost:3000/secured
```
You should receice a message that indicates that you are not authorized.
Ok, we will be back to this shortly.
Now, navigate to ```localhost:8000```, you will be prompted to a log in page served by keycloak. 
Log in as the user you have created previously.
Once logged in, the frontend presents few buttons to click.
What happend behind the cover is that you got a JWT token from keycloak and you are using it now to query the frontend.
Now, click the ```invoke the service``` button.
That button calls the secure backend API (```localhost:3000/secured```) using the JWT token provided from the frontend.
It should return ```a secret message```. 
This is the same API that you were not allowed to query before!
What happened is that the frontend invoked the backend service using the same JWT token that was provided by KeyCloak.





