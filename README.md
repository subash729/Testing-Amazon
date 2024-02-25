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
yarn create vite my-crud-app --template react
cd my-crud-app
yarn add react-router-dom axios
yarn add react-datepicker
yarn add json-server
```

<p align="center">
<img src="https://github.com/LF-DevOps-Training/feb-19-reactjs-prayatna-subash729/blob/main/materials/Q1-T1-1-project-creating.jpg">
</p>

### step -2 : Adding component in react and interconnecting them.

### Database creation
creating db.json in root directory
```bash
{
    "internMembers": []
  }
```
making live to listen 
```bash
json-server --watch db.json --port 3001

```
we can find our database at
http://localhost:3001/internMembers

<p align="center">
<img src="https://github.com/LF-DevOps-Training/feb-19-reactjs-prayatna-subash729/blob/main/materials/Q1-T1-2-databse-up.jpg">
</p>

### Step - 3 : making  some changes so that it can support jsx
3.1  creating **tsconfig.json** in root level
```bash
{
    "compilerOptions": {
      "jsx": "react-jsx"
    }
  }
```

3.2 Adding ``` jsx: 'react-jsx' ``` in vite.config.js
final config will be
```bash
import { defineConfig } from 'vite';
import react from '@vitejs/plugin-react';

// https://vitejs.dev/config/
export default defineConfig({
  plugins: [react()],
  jsx: 'react-jsx', 
})
```
Adding config files
<p align="center">
<img src="https://github.com/LF-DevOps-Training/feb-19-reactjs-prayatna-subash729/blob/main/materials/Q1-T1-3-adding-config-files.jpg">
</p>

### Step -4 Creating ```components``` directory under ```src``` to add components for inserting data
```bash
import React, { useState } from 'react';
import DatePicker from 'react-datepicker';
import 'react-datepicker/dist/react-datepicker.css';
import axios from 'axios';

const InternMembersForm = () => {
  const [formData, setFormData] = useState({
    name: '',
    address: '',
    dateOfBirth: new Date(), // Set a default date
    selectionStatus: false,
  });

  const handleChange = (e) => {
    setFormData({ ...formData, [e.target.name]: e.target.value });
  };

  const handleDateChange = (date) => {
    setFormData({ ...formData, dateOfBirth: date });
  };

  const handleSubmit = async (e) => {
    e.preventDefault();
    try {
      await axios.post('http://localhost:3001/internMembers', formData);
      console.log('Data submitted successfully');
      // You can also redirect or show a success message here
    } catch (error) {
      console.error('Error submitting data:', error);
    }
  };

  return (
    <form onSubmit={handleSubmit}>
      <label>Name:</label>
      <input type="text" name="name" value={formData.name} onChange={handleChange} />

      <label>Address:</label>
      <input type="text" name="address" value={formData.address} onChange={handleChange} />

      <label>Date of Birth:</label>
      <DatePicker selected={formData.dateOfBirth} onChange={handleDateChange} />

      <label>Selection Status:</label>
      <input
        type="checkbox"
        name="selectionStatus"
        checked={formData.selectionStatus}
        onChange={handleChange}
      />

      <button type="submit">Submit</button>
    </form>
  );
};

export default InternMembersForm;

```
### step -5 : Updating App.jsx by routing it to InternMembersForm component 

```bash
import React from 'react';
import InternMembersForm from './components/InternMembersForm';

const App = () => {
  return (
    <div>
      <h1>Intern Members CRUD App</h1>
      <InternMembersForm />
    </div>
  );
};

export default App;
```




### Step -6 Running the Project

```bash
yarn
yarn dev
```
starting the yarn to test project
<p align="center">
<img src="https://github.com/LF-DevOps-Training/feb-19-reactjs-prayatna-subash729/blob/main/materials/Q1-T1-4-running-project.jpg">
</p>

output
<p align="center">
<img src="https://github.com/LF-DevOps-Training/feb-19-reactjs-prayatna-subash729/blob/main/materials/Q1-T5-testing-insert-details-feature.jpg">
</p>

# Q2. Create a table route to show the list of internmembers

InternMembersTable.jsx
Adding package for routing
```bash
yarn add react-router-dom
```
Adding comonents  to shows inserted data
```bash
import React, { useEffect, useState } from 'react';
import axios from 'axios';

const InternMembersTable = () => {
  const [internMembers, setInternMembers] = useState([]);

  useEffect(() => {
    const fetchData = async () => {
      try {
        const response = await axios.get('http://localhost:3001/internMembers');
        setInternMembers(response.data);
      } catch (error) {
        console.error('Error fetching data:', error);
      }
    };

    fetchData();
  }, []);

  return (
    <div>
      <h2>Intern Members List</h2>
      <table>
        <thead>
          <tr>
            <th>Name</th>
            <th>Address</th>
            <th>Date of Birth</th>
            <th>Selection Status</th>
          </tr>
        </thead>
        <tbody>
          {internMembers.map((member) => (
            <tr key={member.id}>
              <td>{member.name}</td>
              <td>{member.address}</td>
              <td>{new Date(member.dateOfBirth).toLocaleDateString()}</td>
              <td>{member.selectionStatus ? 'Selected' : 'Not Selected'}</td>
            </tr>
          ))}
        </tbody>
      </table>
    </div>
  );
};

export default InternMembersTable;

```
#update done on sunday
## still researching and testing

Added component for route to show table entry but while doing changes on App.jsx and main.jsx it doesn't reflect anythin
