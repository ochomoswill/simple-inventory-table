# Creating a Table with dynamic rows and an editable column in React

## Introduction
Tables are critical elements for displaying data in Web applications. 
They are often complemented by forms on a different page or on a modal.
However, at times, creating a  form is hectic when you just need to edit just a column.
Hence, I would like to make developers aware of how to create editable tables to reduce the hassle of having to build a form for the table.
An advantage of this approach, is that you also get to improve your applications UX by minimizing the interfaces a user has to interact with.

In addition to that, tables need a source for the data they are to display. Data can be sourced from a json file or a rest endpoint or just set statically using
 the table data HTML tag. 
You will also learn how to load data dynamically populating table rows, ensuring your tables can always have updated data.

![Final Product](https://drive.google.com/uc?export=view&id=1ign5IPynCM8TIt9Gw6nhi1IgnXQ8504A)

You are going to build a simple inventory table, with the Unit Price column editable to enable updating of the price of a product. 
The table will source its data from a Mock API, which you shall also get to create. You can refer to my previous article on 
[Creating a Mock API in React](https://www.pluralsight.com/guides/react-mock-api)

This guide assumes that you are familiar with  HTML(`table` element), React Hooks, Creating of a Mock API and making API calls using fetch.

## Setup

Setup is split into two steps:
* Creating the Mock API
* Setting Up the Simple Inventory Table React App

### Creating the Mock API

Set up the Mock API as explained in my previous article, [Creating a Mock API in React](https://www.pluralsight.com/guides/react-mock-api). 

Then, when you are done, replace the content of the db.json with the below:

```json
{
  "inventory": [
    {
      "id": 1,
      "product_name": "Weetabix",
      "product_category": "Cereal",
      "unit_price": "501",
      "quantity": 10,
      "date_created": "28-10-2020 00:00:00",
      "date_modified": "29-10-2020 00:00:00"
    },
    {
      "id": 2,
      "product_name": "Colgate Toothpaste",
      "product_category": "Toiletries",
      "unit_price": "119",
      "quantity": 100,
      "date_created": "28-10-2020 00:00:00",
      "date_modified": "29-10-2020 00:00:00"
    },
    {
      "id": 3,
      "product_name": "Imperial Leather Soap",
      "product_category": "Toiletries",
      "unit_price": "235",
      "quantity": 50,
      "date_created": "28-10-2020 00:00:00",
      "date_modified": "29-10-2020 00:00:00"
    },
    {
      "id": 4,
      "product_name": "Sunlight Detergent",
      "product_category": "Toiletries",
      "unit_price": "401",
      "quantity": 20,
      "date_created": "28-10-2020 00:00:00",
      "date_modified": "29-10-2020 00:00:00"
    }
  ]
}
```

To start up your API, run the command below:

```bash
json-server --watch src/db.json
```

You should see be able to see your API running with an endpoint, `http:/localhost:3000/inventory`

### Setting Up the Simple Inventory Table React App

You are going to use Create-React-App. Create-React-App is a scaffold that lets you create react apps with no build configurations.

Ensure you have `create-react-app` installed on your machine. If not, you can install by running the following:

```bash
npm install -g create-react-app
```

Once it is installed, to create the app, run the following:

```bash
npx create-react-app simple-inventory-table
```

The above command creates a react app with the name `simple-inventory-table`

Navigate to your project's root directory. 

```bash
cd simple-inventory-table
```

Your folder structure should be like this:

```
simple-inventory-table/    
    node_modules/
    public/
    src/   
    	App.css
        App.js
        App.test.js
        index.css
        index.js
        logo.svg
        serviceWorker.js
        setupTests.js
    package.json
    README.md
    yarn.lock
```

To start your app, run the following:

```bash
yarn start
```

## Development

The development will be split into the following steps:
* Creating the table
* Populating the table
* Making the table editable

### Creating the table
Open the App.js file. Clear everything inside and paste in the following:

```jsx
import React from 'react';
import './App.css';

function App() {
    return (
        <div className="container">
            <h1>Simple Inventory Table</h1>
            <table>
                <thead>
                <tr>
                    <th>Product Name</th>
                    <th>Product Category</th>
                    <th>Unit Price</th>
                    <th>Quantity</th>
                    <th>Date Created</th>
                    <th>Date Modified</th>
                    <th>Action</th>
                </tr>
                </thead>
                <tbody>
                    <tr>
                        <td/>
                        <td/>
                        <td/>
                        <td/>
                        <td/>
                        <td/>
                        <td/>
                    </tr>
                </tbody>
            </table>
        </div>
    );
}

export default App;
```
Above, you have defined a div with classname container to house the table. You have defined the title of the page
using H1 tag, `Simple Inventory Table`. You have then defined the table with the headers:
`Product Name`, `Product Category`, `Unit Price`, `Quantity`, `Date Created`, `Date Modified`, `Action`.
The table body has one table row with empty table data elements.


Open the App.css file and paste in the following:

```css
.container {
  background-color: #ffffff;
  min-height: 100vh;
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  font-size: calc(10px + 2vmin);
}

table, th, td {
  border: 1px solid black;
  border-collapse: collapse;
  text-align: left;
  padding: 10px;
}
```
The above code, is to style the App. Defining that the container has a white background with a minimum height of 100 viewport height. Items in the container are aligned in the center both vertically and horizontally.
The css code gives the table a border of color black and 1 pixel width, with text left aligned.

### Populating the table

To populate data, you are going to make a GET request to the Mock API URL you created. 
Define the API_HOST of your Mock API and the endpoint. This is done outside the App function:

```jsx
import React from 'react';
import './App.css';

const API_HOST = "http://localhost:3000";
const INVENTORY_API_URL = `${API_HOST}/inventory`;

function App() {
    ...
}
```

You are going to use React Hooks to define state and lifecycles on the App. You will start by defining a state variable to store the data from the Mock API. The default value is set to an empty array.

```jsx
import React, {useState} from 'react';
import './App.css';

const API_HOST = "http://localhost:3000";
const INVENTORY_API_URL = `${API_HOST}/inventory`;

function App() {
    // data state variable defaulted to an empty array
    const [data, setData] = useState([]);
    ...
}
```

Using fetch, you are going to define a function to make a GET request to your Mock API. The setData function is used to update the data state variable on getting
a successful response from the Mock API.

```jsx
import React, {useState} from 'react';
import './App.css';

const API_HOST = "http://localhost:3000";
const INVENTORY_API_URL = `${API_HOST}/inventory`;

function App() {
    const [data, setData] = useState([]);
    // GET request function to your Mock API
    const fetchInventory = () => {
        fetch(`${INVENTORY_API_URL}`)
            .then(res => res.json())
            .then(json => setData(json));
    }
    ...
}
```

You can then call the function on component mount to get the inventory data to populate the table. 
You then iterate through the data, using `.map()` creating the rows dynamically.
```jsx
import React, {useEffect, useState} from 'react';
import './App.css';

const API_HOST = "http://localhost:3000";
const INVENTORY_API_URL = `${API_HOST}/inventory`;

function App() {
    const [data, setData] = useState([]);

    // GET request function to your Mock API
    const fetchInventory = () => {
        fetch(`${INVENTORY_API_URL}`)
            .then(res => res.json())
            .then(json => setData(json));
    }
    
    // Calling the function on component mount
    useEffect(() => {
        fetchInventory();
    }, []);


    return (
        <div className="container">
            <h1>Simple Inventory Table</h1>
            <table>
                <thead>
                <tr>
                    <th>Product Name</th>
                    <th>Product Category</th>
                    <th>Unit Price</th>
                    <th>Quantity</th>
                    <th>Date Created</th>
                    <th>Date Modified</th>
                    <th>Action</th>
                </tr>
                </thead>
                <tbody>
                    {
                        data.map((item) => (
                            <tr key={item.id}>
                                <td>{item.product_name}</td>
                                <td>{item.product_category}</td>
                                <td>{item.unit_price}</td>
                                <td>{item.quantity}</td>
                                <td>{item.date_created}</td>
                                <td>{item.date_modified}</td>
                                <td/>
                            </tr>
                        ))
                    }
                </tbody>
            </table>
        </div>
    );
}

export default App;
```

### Making the table editable
You define a state variable `inEditMode` to track the edit status. It is an object with two properties:
* `status` which is a boolean. Shows whether the table is in edit mode or not
* `rowKey` - is the id of the product being edited, this helps in knowing which row on the table is being edited.

Define a state variable `unitPrice` to hold the unit price of the row being edited. 

Define a function `onEdit` which takes in an object as an argument. The object has two properties:
* `id` - The id of the product of the row being edited
* `currentUnitPrice` -  The current unit price of the product(row being edited). 
    It is used to set the unitPrice state variable which initializes the input visible on edit mode.
    
Define a function `onCancel` which resets the `inEditMode` and `unitPrice` state variables. 
    
Define a function `updateInventory` which takes in an object as an argument. The object has two properties:
* `id` - The id of the product of the row being edited
* `newUnitPrice` -  The updated unit price of the product(row being edited). 
The function sends a PATCH request to our Mock API, updating it with the new unit price. 
On successful update, the `inEditMode` and `unitPrice` state variables are reset and the updated list of inventory fetched.

Define a function `onSave` which takes in an object as an argument. The object has two properties:
* `id` - The id of the product of the row being edited
* `newUnitPrice` -  The updated unit price of the product(row being edited). 
The function calls the `updateInventory` function to update the unit price

```jsx
import React, {useEffect, useState} from 'react';
import './App.css';

const API_HOST = "http://localhost:3000";
const INVENTORY_API_URL = `${API_HOST}/inventory`;

function App() {
    ...

    const [inEditMode, setInEditMode] = useState({
        status: false,
        rowKey: null
    });

    const [unitPrice, setUnitPrice] = useState(null);

    /**
     *
     * @param id - The id of the product
     * @param currentUnitPrice - The current unit price of the product
     */
    const onEdit = ({id, currentUnitPrice}) => {
        setInEditMode({
            status: true,
            rowKey: id
        })
        setUnitPrice(currentUnitPrice);
    }

    /**
     *
     * @param id
     * @param newUnitPrice
     */
    const updateInventory = ({id, newUnitPrice}) => {
        fetch(`${INVENTORY_API_URL}/${id}`, {
            method: "PATCH",
            body: JSON.stringify({
                unit_price: newUnitPrice
            }),
            headers: {
                "Content-type": "application/json; charset=UTF-8"
            }
        })
            .then(response => response.json())
            .then(json => {
                // reset inEditMode and unit price state values
                onCancel();

                // fetch the updated data
                fetchInventory();
            })
    }

    /**
     *
     * @param id -The id of the product
     * @param newUnitPrice - The new unit price of the product
     */
    const onSave = ({id, newUnitPrice}) => {
        updateInventory({id, newUnitPrice});
    }

    const onCancel = () => {
        // reset the inEditMode state value
        setInEditMode({
            status: false,
            rowKey: null
        })
        // reset the unit price state value
        setUnitPrice(null);
    }

    ...
}

export default App;
```

The final code should be like this:
```jsx
import React, {useEffect, useState} from 'react';
import './App.css';

const API_HOST = "http://localhost:3000";
const INVENTORY_API_URL = `${API_HOST}/inventory`;

function App() {
    const [data, setData] = useState([]);

    const fetchInventory = () => {
        fetch(`${INVENTORY_API_URL}`)
            .then(res => res.json())
            .then(json => setData(json));
    }

    useEffect(() => {
        fetchInventory();
    }, []);


    const [inEditMode, setInEditMode] = useState({
        status: false,
        rowKey: null
    });

    const [unitPrice, setUnitPrice] = useState(null);

    /**
     *
     * @param id - The id of the product
     * @param currentUnitPrice - The current unit price of the product
     */
    const onEdit = ({id, currentUnitPrice}) => {
        setInEditMode({
            status: true,
            rowKey: id
        })
        setUnitPrice(currentUnitPrice);
    }

    /**
     *
     * @param id
     * @param newUnitPrice
     */
    const updateInventory = ({id, newUnitPrice}) => {
        fetch(`${INVENTORY_API_URL}/${id}`, {
            method: "PATCH",
            body: JSON.stringify({
                unit_price: newUnitPrice
            }),
            headers: {
                "Content-type": "application/json; charset=UTF-8"
            }
        })
            .then(response => response.json())
            .then(json => {
                // reset inEditMode and unit price state values
                onCancel();

                // fetch the updated data
                fetchInventory();
            })
    }

    /**
     *
     * @param id -The id of the product
     * @param newUnitPrice - The new unit price of the product
     */
    const onSave = ({id, newUnitPrice}) => {
        updateInventory({id, newUnitPrice});
    }

    const onCancel = () => {
        // reset the inEditMode state value
        setInEditMode({
            status: false,
            rowKey: null
        })
        // reset the unit price state value
        setUnitPrice(null);
    }

    return (
        <div className="container">
            <h1>Simple Inventory Table</h1>
            <table>
                <thead>
                <tr>
                    <th>Product Name</th>
                    <th>Product Category</th>
                    <th>Unit Price</th>
                    <th>Quantity</th>
                    <th>Date Created</th>
                    <th>Date Modified</th>
                    <th>Action</th>
                </tr>
                </thead>
                <tbody>
                {
                    data.map((item) => (
                        <tr key={item.id}>
                            <td>{item.product_name}</td>
                            <td>{item.product_category}</td>
                            <td>
                                {
                                    inEditMode.status && inEditMode.rowKey === item.id ? (
                                        <input value={unitPrice}
                                               onChange={(event) => setUnitPrice(event.target.value)}
                                        />
                                    ) : (
                                        item.unit_price
                                    )
                                }
                            </td>
                            <td>{item.quantity}</td>
                            <td>{item.date_created}</td>
                            <td>{item.date_modified}</td>
                            <td>
                                {
                                    inEditMode.status && inEditMode.rowKey === item.id ? (
                                        <React.Fragment>
                                            <button
                                                className={"btn-success"}
                                                onClick={() => onSave({id: item.id, newUnitPrice: unitPrice})}
                                            >
                                                Save
                                            </button>

                                            <button
                                                className={"btn-secondary"}
                                                style={{marginLeft: 8}}
                                                onClick={() => onCancel()}
                                            >
                                                Cancel
                                            </button>
                                        </React.Fragment>
                                    ) : (
                                        <button
                                            className={"btn-primary"}
                                            onClick={() => onEdit({id: item.id, currentUnitPrice: item.unit_price})}
                                        >
                                            Edit
                                        </button>
                                    )
                                }
                            </td>
                        </tr>
                    ))
                }
                </tbody>
            </table>
        </div>
    );
}

export default App;
```

On the table, you added ternary operator on the `Unit Price` and `Action` columns.

On the `Unit Price` column:
* when in edit mode, an input in visible to enable editing of the unit price
* else: the unit price value is visible.


On the `Action` column:
* when in edit mode, a `Save` and `Cancel` button is visible
    * `Save` - Submits the updated unit price value to the Mock API
    * `Cancel` - resets the row from edit mode
* else: `Edit` button is visible which when clicked makes the row editable.

The final css with the styling of the `input` and the `buttons` is as follows:

```css
.container {
  background-color: #ffffff;
  min-height: 100vh;
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  font-size: calc(10px + 2vmin);
}

table, th, td {
  border: 1px solid black;
  border-collapse: collapse;
  text-align: left;
  padding: 10px;
}

input{
  height: 40px;
  font-size: 24px;
  width: 120px;
}

button{
  color: #ffffff;
  border: none;
  outline: none;
  padding: 5px 14px;
  font-size: 24px;
  border-radius: 4px;
}

.btn-primary{
  background: #0050ef;
}

.btn-secondary{
  background: #647687;
}

.btn-success{
  background: #008a00;
}

```

## Conclusion
There you have it. A table sourcing data from a Mock API which is used to dynamically create the rows. 
You also went ahead to make a column editable. Aside from that, as a supplement of my previous article, you were able to make a PATCH request to 
a Mock API. Don't hesitate to further explore and exhaust how you can extend your Mock APIs using *json-server*. 
You could also go ahead and make all the columns editable.
