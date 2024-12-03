To set up the project with all necessary configurations, follow these steps:

1. **Initialize the project**
2. **Set up Django**
3. **Set up React**
4. **Configure backend and frontend integration**
5. **Install necessary dependencies**
6. **Create and configure database**
7. **Set up environment variables**

### Step-by-Step Guide

#### 1. Initialize the Project

**Create project directory:**
```bash
mkdir crm-system
cd crm-system
```

#### 2. Set Up Django

**Create a Django project:**
```bash
python3 -m venv venv
source venv/bin/activate
pip install django djangorestframework
django-admin startproject backend
cd backend
django-admin startapp users
django-admin startapp products
django-admin startapp orders
django-admin startapp sales
django-admin startapp ebms
django-admin startapp reports
django-admin startapp stock
django-admin startapp customers
django-admin startapp company
```

**Install additional Django dependencies:**
```bash
pip install djangorestframework-simplejwt
pip install django-cors-headers
```

**Configure `settings.py`:**
```python
# backend/settings.py

INSTALLED_APPS = [
    ...
    'rest_framework',
    'corsheaders',
    'users',
    'products',
    'orders',
    'sales',
    'ebms',
    'reports',
    'stock',
    'customers',
    'company',
]

MIDDLEWARE = [
    ...
    'corsheaders.middleware.CorsMiddleware',
]

CORS_ALLOWED_ORIGINS = [
    "http://localhost:3000",
]

REST_FRAMEWORK = {
    'DEFAULT_AUTHENTICATION_CLASSES': (
        'rest_framework_simplejwt.authentication.JWTAuthentication',
    ),
}

# JWT settings
from datetime import timedelta
SIMPLE_JWT = {
    'ACCESS_TOKEN_LIFETIME': timedelta(minutes=30),
    'REFRESH_TOKEN_LIFETIME': timedelta(days=1),
}

# Database settings
DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.sqlite3',
        'NAME': BASE_DIR / "db.sqlite3",
    }
}
```

**Add URL configurations:**
```python
# backend/urls.py

from django.contrib import admin
from django.urls import path, include
from rest_framework_simplejwt.views import TokenObtainPairView, TokenRefreshView

urlpatterns = [
    path('admin/', admin.site.urls),
    path('api/token/', TokenObtainPairView.as_view(), name='token_obtain_pair'),
    path('api/token/refresh/', TokenRefreshView.as_view(), name='token_refresh'),
    path('api/users/', include('users.urls')),
    path('api/products/', include('products.urls')),
    path('api/orders/', include('orders.urls')),
    path('api/sales/', include('sales.urls')),
    path('api/ebms/', include('ebms.urls')),
    path('api/reports/', include('reports.urls')),
    path('api/stock/', include('stock.urls')),
    path('api/customers/', include('customers.urls')),
    path('api/company/', include('company.urls')),
]
```

**Create URLs for each app:**
For example, `users/urls.py`:
```python
# users/urls.py

from django.urls import path, include
from rest_framework.routers import DefaultRouter
from .views import UserViewSet

router = DefaultRouter()
router.register(r'', UserViewSet)

urlpatterns = [
    path('', include(router.urls)),
]
```

Repeat the above URL configuration for other apps similarly.

#### 3. Set Up React

**Navigate to the project root and create React app:**
```bash
cd ..
npx create-react-app frontend
cd frontend
```

**Install necessary dependencies:**
```bash
npm install axios redux react-redux @reduxjs/toolkit react-router-dom
```

#### 4. Configure Backend and Frontend Integration

**Add a proxy to `package.json` in React app:**
```json
// frontend/package.json

"proxy": "http://127.0.0.1:8000",
```

**Set up Redux store and slices (example for user slice):**
```javascript
// frontend/src/store/userSlice.js

import { createSlice, createAsyncThunk } from '@reduxjs/toolkit';
import axios from 'axios';

export const fetchUsers = createAsyncThunk('users/fetchUsers', async () => {
    const response = await axios.get('/api/users/');
    return response.data;
});

const userSlice = createSlice({
    name: 'users',
    initialState: {
        users: [],
        status: null,
    },
    extraReducers: {
        [fetchUsers.pending]: (state) => {
            state.status = 'loading';
        },
        [fetchUsers.fulfilled]: (state, action) => {
            state.status = 'succeeded';
            state.users = action.payload;
        },
        [fetchUsers.rejected]: (state) => {
            state.status = 'failed';
        },
    },
});

export default userSlice.reducer;
```

**Configure the Redux store:**
```javascript
// frontend/src/store/store.js

import { configureStore } from '@reduxjs/toolkit';
import userReducer from './userSlice';

export default configureStore({
    reducer: {
        users: userReducer,
    },
});
```

**Wrap the app with the provider:**
```javascript
// frontend/src/index.js

import React from 'react';
import ReactDOM from 'react-dom';
import { Provider } from 'react-redux';
import store from './store/store';
import App from './App';

ReactDOM.render(
    <Provider store={store}>
        <App />
    </Provider>,
    document.getElementById('root')
);
```

#### 5. Install Necessary Dependencies

Already covered in the above steps (e.g., `pip install django`, `npm install axios redux`).

#### 6. Create and Configure Database

**Run migrations:**
```bash
cd backend
python manage.py makemigrations
python manage.py migrate
python manage.py createsuperuser
```

#### 7. Set Up Environment Variables

Create a `.env` file in the root of the backend project to store sensitive data:
```
SECRET_KEY=your_secret_key
DEBUG=True
DB_NAME=db_name
DB_USER=db_user
DB_PASSWORD=db_password
DB_HOST=db_host
DB_PORT=db_port
```

**Update `settings.py` to use environment variables:**
```python
import os
from pathlib import Path
from dotenv import load_dotenv

load_dotenv()

SECRET_KEY = os.getenv('SECRET_KEY')
DEBUG = os.getenv('DEBUG') == 'True'
DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.postgresql',
        'NAME': os.getenv('DB_NAME'),
        'USER': os.getenv('DB_USER'),
        'PASSWORD': os.getenv('DB_PASSWORD'),
        'HOST': os.getenv('DB_HOST'),
        'PORT': os.getenv('DB_PORT'),
    }
}
```

### Final Steps

1. **Run the backend server:**
   ```bash
   cd backend
   python manage.py runserver
   ```

2. **Run the frontend development server:**
   ```bash
   cd frontend
   npm start
   ```

With these steps, you should have a basic structure and setup for your CRM system. You can further extend and refine each module by adding more specific functionality and improving the UI/UX.# Enhancement log for aebus on Tue Dec  3 09:07:39 PM UTC 2024
