## Description
Create a CRUD application in React interacting with a json-server based on the requirements below

#### Data should have fields for intern members 
1. Name
2. Address
3. Date of birth
4. Selection Status(boolean)

### 1. Create a form route to entry data for the internmembers
# Answer
### Step -1 : Creating project
```
npm create vite@latest
cd subash-app
npm install
npm run dev
```
<p align="center">
<img src="https://github.com/LF-DevOps-Training/feb-19-reactjs-prayatna-subash729/blob/main/materials/Q1-T1-1-project-creating.jpg">
</p>

Testing Project output

<p align="center">
<img src="https://github.com/LF-DevOps-Training/feb-19-reactjs-prayatna-subash729/blob/main/materials/Q1-T1-1.1-project-open.jpg">
</p>

### step -2 : Adding component in react and interconnecting them.
Error faced by me on manual entry of data of intern team
<p align="center">
<img src="https://github.com/LF-DevOps-Training/feb-19-reactjs-prayatna-subash729/blob/main/materials/Q1-T1-1.2-old-error.jpg">
</p>

Then Researching and and creating new project
```
npx create-react-app intern-members-app
cd intern-members-app
npm install -g json-server
npm install axios react-router-dom
npm install bootstrap-react bootstrap
npm start
```

Now,code of react to develop form component and default app are as follows 

form code : [form](https://github.com/LF-DevOps-Training/feb-19-reactjs-prayatna-subash729/blob/main/code/InternMembersForm.js)

App code : [App](https://github.com/LF-DevOps-Training/feb-19-reactjs-prayatna-subash729/blob/main/code/App.js)

### Output
<p align="center">
<img src="https://github.com/LF-DevOps-Training/feb-19-reactjs-prayatna-subash729/blob/main/materials/Q1-T1-2-form-layout.jpg">
</p>


