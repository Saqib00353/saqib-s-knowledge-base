# Redux Toolkit Query API Configuration

This file demonstrates how to set up a basic API using Redux Toolkit's `createApi` function.

```javascript
const api = createApi({
  reducerPath: "api",
  baseQuery: fetchBaseQuery({ baseUrl: "https://api.example.com" }),
  endpoints: (builder) => ({
    getUser: builder.query({
      query: (id) => `/users/${id}`,
    }),
  }),
})
```

## Key Points:

1. `createApi` is used to define an API slice.
2. `reducerPath` specifies where the API slice will be stored in the Redux state.
3. `baseQuery` sets up the base configuration for all requests, using `fetchBaseQuery`.
4. `endpoints` define the various operations that can be performed with this API.
5. Each endpoint (like `getUser`) specifies how to construct the query URL.

This setup can be expanded with more endpoints, error handling, and request transformation as needed.

#Example

```javascript
// baseQuery.js with interceptors
import { StoreHelper } from "@/utilities/helpers/storeHelpers"
import { fetchBaseQuery } from "@reduxjs/toolkit/query/react"

// Request Interceptor
const baseQuery = fetchBaseQuery({
  baseUrl: "https://api.example.com",
  prepareHeaders: (headers) => {
    const token = StoreHelper.get("accessToken")
    if (token) headers.set("x-access-token", token)
    headers.set("withCredentials", true)
    return headers
  },
})

// Response Interceptor
const baseQueryWithInterceptor = async (args, api, extraOptions) => {
  let result = await baseQuery(args, api, extraOptions)

  if (result.error && result.error.status === 498 && result.error.data.message === "Invalid or expired token") {
    const refreshToken = StoreHelper.get("refreshToken")
    if (refreshToken) {
      const refreshResult = await baseQuery(
        {
          url: "/auth/refresh/tokens",
          method: "POST",
          headers: {
            "x-refresh-token": refreshToken,
          },
        },
        api,
        extraOptions
      )
      const access = refreshResult.meta?.response?.headers.get("x-access-token")
      const refresh = refreshResult.meta?.response?.headers.get("x-refresh-token")
      if (access && refresh) {
        StoreHelper.set("accessToken", access)
        StoreHelper.set("refreshToken", refresh)
        return baseQuery(args, api, extraOptions)
      }
    }
  } else if (result.data) {
    const accessToken = result.meta?.response?.headers.get("x-access-token")
    const refreshToken = result.meta?.response?.headers.get("x-refresh-token")

    if (accessToken && refreshToken) {
      StoreHelper.set("accessToken", accessToken)
      StoreHelper.set("refreshToken", refreshToken)
    }
  }

  return result
}

export default baseQueryWithInterceptor

// authServices.js
export const login = (body) => ({
  url: "/auth/login",
  method: "POST",
  body,
})

export const register = (body) => ({
  url: "/user/register",
  method: "POST",
  body,
})

export const forgotPassword = (body) => ({
  url: "/auth/forgot-password",
  method: "POST",
  body,
})

export const resetPassword = (body) => ({
  url: "/auth/password/update",
  method: "POST",
  body,
})

// authApi.js
import { createApi } from "@reduxjs/toolkit/query/react"
import { login, register, forgotPassword } from "@/redux/api/services/authServices"
import baseQueryWithInterceptor from "./baseQuery"

export const authApi = createApi({
  reducerPath: "authApi",
  baseQuery: baseQueryWithInterceptor,
  endpoints: (builder) => ({
    login: builder.mutation({
      query: login,
      invalidatesTags: ["User"],
    }),
    register: builder.mutation({
      query: register,
      invalidatesTags: ["User"],
    }),
    forgotPassword: builder.mutation({
      query: forgotPassword,
    }),
    getUser: builder.query({
      query: () => "/user/me",
      providesTags: ["User"],
    }),
  }),
})

export const { useLoginMutation, useRegisterMutation, useForgotPasswordMutation, useGetUserQuery } = authApi
```
