# ProtectedRoute Component

`ProtectedRoute` component, which is used to protect routes based on authentication status.

```javascript
  ## Imports

  import { useSelector } from "react-redux"
  import { Navigate, useLocation } from "react-router-dom"

  ## Component Definition

  const ProtectedRoute = ({ children, authRoute = false }) => {
    const isAuthenticated = useSelector((state) => state.user.isAuthenticated)
    const location = useLocation()

    if (authRoute) {
      return isAuthenticated ? <Navigate to="/" replace /> : children
    }

    return isAuthenticated ? children : <Navigate to="/auth/login" replace state={{ from: location }} />
  }

  export default ProtectedRoute
```

This component can be imported and used in other parts of the application to protect routes that require authentication.
