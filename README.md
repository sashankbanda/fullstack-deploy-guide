# fullstack-deploy-guide
A step-by-step guide to building and deploying a full-stack project using a React frontend (Vite), a Flask backend, and deployment on Render (backend) &amp; Vercel (frontend).

### **Step-by-Step Guide to Build & Deploy Your Full-Stack Project**  
You have:  
- **Backend**: `app.py` (likely a Flask or FastAPI app)  
- **Frontend**: `App.js`, `styles.css`, `script.js` (currently in plain JavaScript)  

Now, we’ll:  
1. **Set up the Vite React frontend**  
2. **Integrate your existing frontend code into React**  
3. **Set up the backend with Render deployment**  
4. **Deploy the React frontend on Vercel**  

---

## **Step 1: Setting Up a Vite React Project**
Vite is a modern frontend tool for React that is fast and easy to configure.  

### **1.1 Install Node.js and npm**  
Ensure you have **Node.js** (LTS version) installed. Check with:  
```sh
node -v
npm -v
```
If not installed, download from [Node.js official site](https://nodejs.org/).

---

### **1.2 Create a New Vite React Project**
Run the following command to create a Vite project with React:  
```sh
npm create vite@latest my-frontend --template react
```
This sets up a React app inside `my-frontend/`.

---

### **1.3 Navigate & Install Dependencies**  
```sh
cd my-frontend
npm install
```

---

### **1.4 Start Development Server**  
```sh
npm run dev
```
It should open a browser at `http://localhost:5173/`.

---

## **Step 2: Integrating Your Existing Frontend Code**  

### **2.1 Replace `App.js` Content**  
Since Vite uses `.jsx`, rename `App.js` → `App.jsx` and replace its content:  
```jsx
import { useState } from 'react';
import './styles.css';

function App() {
  const [data, setData] = useState(null);

  const fetchData = async () => {
    const response = await fetch('https://your-backend-url.onrender.com/api/data');
    const result = await response.json();
    setData(result);
  };

  return (
    <div className="container">
      <h1>My React App</h1>
      <button onClick={fetchData}>Fetch Data</button>
      {data && <p>{JSON.stringify(data)}</p>}
    </div>
  );
}

export default App;
```

---

### **2.2 Move `styles.css`**  
Place `styles.css` inside `src/`:  
```css
/* src/styles.css */
.container {
  text-align: center;
  padding: 20px;
}
```
Import it in `App.jsx`:  
```jsx
import './styles.css';
```

---

### **2.3 Convert `script.js` Logic (if needed)**  
If `script.js` contains DOM manipulation (`document.querySelector`), refactor it using React states.

---

### **2.4 Fix Vite React Issues**
Vite uses **ES modules**. If you import local assets, add `import.meta.env.BASE_URL` before file paths.

---

## **Step 3: Setting Up and Deploying Backend on Render**  

### **3.1 Install `gunicorn` (For Production Server)**
In `app.py` (assuming Flask):  
```sh
pip install flask gunicorn
```

---

### **3.2 Create a `requirements.txt`**
Run:  
```sh
pip freeze > requirements.txt
```
Ensure it includes Flask and Gunicorn.

---

### **3.3 Modify `app.py` for Deployment**  
Update `app.py`:  
```python
from flask import Flask, jsonify
from flask_cors import CORS

app = Flask(__name__)
CORS(app)  # To allow frontend requests

@app.route('/api/data', methods=['GET'])
def get_data():
    return jsonify({"message": "Hello from Flask!"})

if __name__ == '__main__':
    app.run(debug=True)
```

---

### **3.4 Create `render.yaml`**
Render uses a `render.yaml` for configuration:  
```yaml
services:
  - type: web
    name: my-backend
    env: python
    buildCommand: pip install -r requirements.txt
    startCommand: gunicorn app:app
    plan: free
```

---

### **3.5 Deploy on Render**  
1. Push your backend code to **GitHub**.  
2. Go to [Render](https://dashboard.render.com/) → **New Web Service**.  
3. Connect your GitHub repo.  
4. Choose environment as **Python 3.x**.  
5. Set **start command**:  
   ```sh
   gunicorn app:app
   ```
6. Click **Deploy**.  

Your backend API URL will be like:  
```
https://my-backend.onrender.com/api/data
```

---

## **Step 4: Deploying React Frontend on Vercel**  

### **4.1 Build the React App**
Inside `my-frontend/`, run:  
```sh
npm run build
```
This generates a `dist/` folder.

---

### **4.2 Deploy on Vercel**
1. Install Vercel CLI:  
   ```sh
   npm install -g vercel
   ```
2. Login to Vercel:  
   ```sh
   vercel login
   ```
3. Navigate to your project folder:  
   ```sh
   cd my-frontend
   ```
4. Deploy:  
   ```sh
   vercel
   ```
5. Follow prompts (select **React**, use default settings).  

Your frontend will be live at:  
```
https://your-project.vercel.app
```

---

### **5. Final Check**
1. Open `https://your-project.vercel.app`.  
2. Click **Fetch Data** and verify it gets data from your backend (`onrender.com`).  

🚀 **Congratulations! Your full-stack app is now deployed!**
