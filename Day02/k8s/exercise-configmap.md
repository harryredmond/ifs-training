## Exercise —

- Create a configMap called 'options' with the value server=local 
- Create a json file with the following content:
```
{
   "username": "YOUR_NAME",
   "password": "PASSWORD",
   "email": "YOUR_EMAIL"
}
```
   - Save the file as secret.json
   - Create a generic secret object from the file
   - Extract the password value from the secret and store it in password.txt

- Validate your resources
