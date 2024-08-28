Mongodb(hello.py):-
from flask import Flask,request
from pymongo import MongoClient


from flask_cors import CORS   #pip install flask_cors

app = Flask(__name__)

cors=CORS(app,resources={r"/*":{"origins":"*"}})

client=MongoClient('mongodb://localhost:27017/')

mdb=client['pro456']

collection = mdb['users']
@app.route("/db/insert")
def dataBase1():
    users =[
    {
        '_id' : 1,
        'name' : "John",
        'dept' : "CSE"
        },
        {
         '_id' : 2,
        'name' : "Rohan",
       'dept' : "CSE"
        },
        {
         '_id' : 3,
        'name' : "Jack",
        'dept' : "CSE"
        },
        {
         '_id' : 4,
        'name' : "Mohan",
       'dept' : "CSE"
        },
        {
         '_id' : 5,
        'name' : "Jojo",
        'dept' : "CSE"
        }
    ]
    collection.insert_many(users)
    return "database inserted"

@app.route("/db/read")
def read():
    userOne = mdb.users.find_one({'_id' : 1})
    users = mdb.users.find({'dept' : "CSE"})
    foundList = []
    for user in users:
        foundList.append(user)
    secondList = []
    for user in foundList:
        secondList.append(user)
    print ('users', users)
    return{'fountList' : foundList, "secondList": secondList, "userOne": userOne}



from flask import jsonify
@app.route("/db/update")
def update():
    user = mdb.users.find_one({'_id': 1})
    updatedUser = mdb.users.update_one({"_id": 1}, {"$set": {"location": "Naurangabad , GT Road"}})
    updateUser= mdb.users.update_one({'_id':1},{'$set':{'email':"thakurlakshya@gmail.com","password":'@dj9149370081'}} )
    return jsonify({"user": user, "updatedUser": {"matched_count": updatedUser.matched_count, "modified_count": updatedUser.modified_count}})




@app.route("/db/remove")
def remove():
    mdb.users.delete_one({"_id": 2})
    return{
        'status':1,
        'message':'user deleted',
        'class': "success"
    }

@app.route("/login", methods=['POST'])
def login1():
    input = request.json
    email = input.get('email')
    password = input.get('password')

    user = mdb.users.find_one({'email': email, 'password': password})

    if user and '_id' in user:
        return {
            'status': 1,
            'msg': "user exits correct detail",
            'class': "success"
        }
    else:
        return {
            'status': 0,
            'msg': "invalid credentials",
            'class': "error"
        }

 React  1:(App.tsx)-
 import "./App.css";
import { BrowserRouter, Route, Routes } from "react-router-dom";
import LoginForm from "./login";
import Table from "./table";
import HomePage  from "./home";
import Counter from "./counter";

const App = () => {
  return (
    <BrowserRouter>
      <Routes>
        <Route path="/home" element={<HomePage />} />
        <Route path="/login" element={<LoginForm />} />
        <Route path="/counter" element={<Counter />} />
        <Route path="/table" element={<Table />} />
      </Routes>
    </BrowserRouter>
  );
};

export default App;

(login.tsx):-
import { useState } from "react";

// import { json } from "../node_modules/react-router-dom/dist/index";

const Login = (props) => {
  const [message, setMessage] = useState(false);
  const [data, setData] = useState({ email: "Initial", password: "initial" });
  const [error, setError] = useState(null);

  const updateData = (type, event) => {
    setData({ ...data, [type]: event });
  };

  const handlesSubmit = async (e) => {
    e.preventDefault();

    const response = await fetch("http://127.0.0.1:5000/login", {
      method: "POST",
      headers: {
        "Content-Type": "application/json",
      },
      body: JSON.stringify(data),
    });
    console.log(data);

    const result = await response.json();
    setMessage("result"?.msg);
    console.log(result);
  };

  return (
    <div style={{ padding: "20px", textAlign: "center" }}>
      <h1>Login Form</h1>

      <div>
        <label>
          Username:
          <input
            type="text"
            onChange={(e) => updateData("text", e.target.value)}
          />
        </label>
      </div>
      <br />
      <div>
        <label>
          email:
          <input
            type="email"
            onChange={(e) => updateData("email", e.target.value)}
          />
        </label>
      </div>
      <br />
      <div>
        <label>
          Number:
          <input
            type="number"
            onChange={(e) => updateData("number", e.target.value)}
          />
        </label>
      </div>
      <br />
      <div>
        <label>
          Password:
          <input
            type="password"
            onChange={(e) => updateData("password", e.target.value)}
          />
        </label>
      </div>
      <br />

      <button onClick={handlesSubmit}>Submit</button>
    </div>
  );
};

export default Login;


(home.tsx):-
import React, { useState } from 'react';
import './App.css';

const HomePage = () => {
  const [data, setData] = useState([]);

  return (
    <div className="home-page">
      <h1>Welcome to My Home Page</h1>
      <p>This is a sample home page.</p>
    </div>
  );
};

export default HomePage;


(table.tsx):-
import { useState } from "react";
 import "./App.css"

 export const Table = () => {
const [data, setData] = useState([]);

   const getData = async () => {
     const response = await fetch("https://jsonplaceholder.typicode.com/users/");
     const responseValue = await response.json();
     console.log(responseValue);
     setData(responseValue);
   };

   const handleRemove = (index: number) => {
     setData(data.filter((_, i) => i !== index));
   };

   return (
     <>
       <button onClick={() => getData()}>Get Data</button>
       <button onClick={() => setData([])}>Reset</button>

       <table>
         <tr>
           <th>S.No</th>
           <th>Name</th>
           <th>User Name</th>
           <th>E-mail</th>
           <th>Phone</th>
           <th>Action</th>
         </tr>
         {data.map((user, index) => (
           <tr key={index}>
             <td>{index + 1}</td>
             <td>{user.name}</td>
             <td>{user.username}</td>
             <td>{user.email}</td>
             <td>{user.phone}</td>
             <td>
               <button onClick={() => handleRemove(index)}>Remove</button>
             </td>
           </tr>
         ))}
       </table>
     </>
);
 };

 export default Table;


 (counter.tsx):-
 const Counter  = (props) => {
    console.log("Counter props:",props)
    
    
        const {buttonLabel, color, count, countButtonClick} = props;
      
        return (
            <>
            <h3> This Button is clicked {count} times</h3>
            <button onClick={() => countButtonClick()}style ={{borderColor: color}}>
                 {buttonLabel} 
                 </button>
            
            </>
        );
     };
    export default Counter;


    


    (App.css):-
    #root {
    max-width: 1280px;
    margin: 0 auto;
    padding: 2rem;
    text-align: center;
  } 
  body {
    font-family: sans-serif;
    background-color: #1234f7;
  }
  
  .container {
    width: 500px;
    margin: 0 auto;
    padding: 20px;
    background-color: #fff;
    border-radius: 5px;
    box-shadow: 0 2px 5px rgba(0, 0, 0, 0.1);
  }
  
  h1 {
    text-align: center;
    margin-bottom: 20px;
  }
  
  label {
    display: block;
    margin-bottom: 5px;
    font-weight: bold;
  }
  
  input[type="text"],
  input[type="password"] {
    width: 100%;
    padding: 10px;
    margin-bottom: 10px;
    border: 1px solid #ddd;
    border-radius: 3px;
  }
  
  button {
    background-color: #4CAF50;
    color: white;
    padding: 10px 20px;
    border: none;
    border-radius: 3px;
    cursor: pointer;
  }
  
  button:hover {
    background-color: #45a049;
  }

  (index.css):-
  :root {
    font-family: Inter, system-ui, Avenir, Helvetica, Arial, sans-serif;
    line-height: 1.5;
    font-weight: 400;

    color-scheme: light dark;
    color: rgba(255, 255, 255, 0.87);
    background-color: #242424;

    font-synthesis: none;
    text-rendering: optimizeLegibility;
    -webkit-font-smoothing: antialiased;
    -moz-osx-font-smoothing: grayscale;
}

a {
    font-weight: 500;
    color: #646cff;
    text-decoration: inherit;
}

a:hover {
    color: #535bf2;
}

body {
    margin: 0;
    display: flex;
    place-items: center;
    min-width: 320px;
    min-height: 100vh;
}

h1 {
    font-size: 3.2em;
    line-height: 1.1;
}

button {
    border-radius: 8px;
    border: 1px solid transparent;
    padding: 0.6em 1.2em;
    font-size: 1em;
    font-weight: 500;
    font-family: inherit;
    background-color: #1a1a1a;
    cursor: pointer;
    transition: border-color 0.25s;
}

button:hover {
    border-color: #646cff;
}

button:focus,
button:focus-visible {
    outline: 4px auto -webkit-focus-ring-color;
}

@media (prefers-color-scheme: light) {
    :root {
        color: #213547;
        background-color: #ffffff;
    }

    a:hover {
        color: #747bff;
    }

    button {
        background-color: #f9f9f9;
    }
}

 
 
