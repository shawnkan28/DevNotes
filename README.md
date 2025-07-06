# DevNotes
I probably include learning examples here so that i dont forget what i already tried and learned during personal project development

# Flask - Python
I'm currently just using Flask as a light-weight server to force file movement when interacting with React. Probably once i'm familiar with React, I'll move on the a more robust backend. <br>
Initialize Flask Environment
```powershell
python -m venv .venv # Initialize virtual environment
& .venv/Scripts/Activate.ps1  # Open terminal in virtual environment
python -m pip install -m pip --upgrade
pip install Flask
flask --app server run   # Start Flask Project, sever is the name of the .py file (server.py)
```
To test API, i use [Bruno](https://www.usebruno.com/)<br>
See code for general usage of Flask
```python
# For getting request with get params
@app.route("/db/get/<string:merch_id>", methods=["GET"])
def get_entry(merch_id):
  return flask.jsonify({"msg": "results"})
```
How to determind if this is dev server or prod server. I typically use the os environment to determine. i use a cmd file to set the os environ to "dev" or "prd" depending on which cmd file i run. Then in python-flask i get which environ using the following:
```python
# "th" being the environ variable that i set "dev" or "prd" to
server_type = os.environ.get("th") 
```
## How to avoid CORS
this should only be used for development only
```powershell
pip install flask-cors
```
```python
from flask import Flask
from flask_cors import CORS

app = Flask(__name__)
CORS(app)  # Allow all origins
# OR
app = Flask(__name__)
CORS(app, origins=["http://localhost:3000"])
```

# React - Javascript/Html
Currently, i'm just trying to remember what i've learned in the past. I'll be starting my React project from the basics.<br>
## useState
useState can be used within the returned DOM in a JSX file as compared to useRef Hook that cannot be used within the DOM. useState will cause a re-render if the values change. See the following on how re-render works with useState.<br>
1. On Render, there is no console.log. The value shown within the div should show "Hello".
2. On Click of the div, the word shown will be changed to "World"
3. But when you check the console.log, it shows "Hello"
4. This is because when user clicks, it hits the handleClick action which sets the variable and print the variable. So the variable will only update after the re-render. but the console.log occurs before the re-render occurs. so which means upon Click, its still holding the previous variable value which is the "Hello".
```javascript
import { useState } from "react";

function Test(){
  const [test, setTest] = useState("Hello");

  const handleClick = () => {
    setTest("World");
    console.log(test)
  }

  return (
    <div onClick={handleClick}>{test}</div>
  )
}
export default Test;
```
## useRef + useImperativeHandle
I typically use this to manipulate the open/close of a modal. I dont want to use useState because it will cause the parent to re-render. i only want the child modal component to re-render. So with useImperativeHandle, we can pass the function from the child component to the parent component. So the parent can call the function and this cause a change in useState of the child component. since only the child component useState variable is changed, the parent component will not be re-rendered. See code E.g.<br>
### Modal.jsx (Child)
```javascript
import { useImperativeHandle, useRef, forwardRef, useState } from "react";
function Modal(_, ref) {
    const modalRef = useRef();
    const [isOpen, setIsOpen] = useState(false);

    useImperativeHandle(ref, () => ({
        toggleModal: () => {
            setIsOpen(!isOpen);
        }
    }))

    return <>
        {isOpen && (
            <>
                <div ref={modalRef} className="modal-background" onClick={() => setIsOpen(false)}></div>
                <div className="modal-wrapper">
                    <div className="modal-body">
                    </div>
                </div>
            </>
        )}
    </>
}
export default forwardRef(Modal);
```
### App.jsx (Parent)
```
import Modal from "./components/Modal";
import { useRef } from "react";

function App() {
  const modalRef = useRef(null);

  const handleOpen = (e) => {
    modalRef.current?.toggleModal(e);
  }

  return (
    <>
      <Navbar onOpenModal={handleOpen}/>
      <Modal ref={modalRef} />
    </>
  )
}

export default App
```
## useEffect
### Note
if you run the app in Dev Mode with <StrictMode> in main.jsx, it will cause a re-render. So useEffects will run twice even though its not suppose to. The function of this re-render is to test for variable mutation and other debugging functions.
### Function
this hook is very useful to load data when screen renders. do note that useEffect is always activated after the initial render. This means there will be a re-render once when useEffects completes. So to ensure the child components is not constantly re-rendering, we only want to render after all data is extracted, we can use Loading useState to control. If data is not loaded, show loading screen, if data is loaded, show everything. See Code E.g.
### App.jsx
```javascript
import { getData } from "./services/db";
function App() {
  console.log("App.jsx is Rendered");
  const [merchList, setMerchList] = useState([]);
  const [isLoading, setIsLoading] = useState(true);

  useEffect(() => {
    const dbList = async () => {
      try {
        const data = await getData();
        console.log(data);
        setMerchList(data);
      } catch (err) {
        console.error("Failed to fetch data:", err);
      }

    }
    dbList();
  }, [])

  if (isLoading) {
    return <div>Loading ...</div>;
  }

  return (
    <>
      <div className="main-container">
        <div className="main-gallery">
          {
            merchList.map(e => {
              return <GalleryItem item={e} key={e.id} onOpenModal={handleOpen}/>
            })
          }
        </div>
      </div>
    </>
  )
}
export default App
```
### db.js
```javascript
export const getData = async () => {
    const port = 5000;
    const url = `http://127.0.0.1:${port}/db/get`;
    const response = await fetch(url);
    const data = await response.json();
    if (response.status == 200) {
        return data.result;
    } else {
        throw new Error(`Status Response from Server: ${response.status}`);
    }
}
```

