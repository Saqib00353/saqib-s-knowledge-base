# useViewport Hook

## Key Points:

- Custom React hook for responsive design
- Tracks window size and provides viewport-based breakpoints
- Uses `useState` and `useEffect` for state management and side effects
- Defines breakpoints for mobile, tablet, laptop, and desktop views
- Returns boolean flags for each viewport size

```javascript
import { useState, useEffect } from "react"

const BREAKPOINTS = {
  mobile: 0,
  tablet: 768,
  laptop: 1024,
  desktop: 1440,
}

function useViewport() {
  const [windowSize, setWindowSize] = useState({
    width: window.innerWidth,
    height: window.innerHeight,
  })

  useEffect(() => {
    function handleResize() {
      setWindowSize({
        width: window.innerWidth,
        height: window.innerHeight,
      })
    }

    window.addEventListener("resize", handleResize)

    return () => {
      window.removeEventListener("resize", handleResize)
    }
  }, [])

  const { width } = windowSize

  const isMobileView = width <= BREAKPOINTS.mobile
  const isTabletView = width > BREAKPOINTS.mobile && width <= BREAKPOINTS.tablet
  const isLaptopView = width > BREAKPOINTS.tablet && width <= BREAKPOINTS.laptop
  const isDesktopView = width > BREAKPOINTS.laptop

  return {
    isMobileView,
    isTabletView,
    isLaptopView,
    isDesktopView,
  }
}

export default useViewport
```
