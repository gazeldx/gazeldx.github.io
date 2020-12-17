---
layout: post
title:  "Material UI"
date:   2020-05-25 21:11:11
categories: Material-UI
---

## Layout
https://material-ui.com/guides/responsive-ui/

https://material.io/design/layout/responsive-layout-grid.html#columns-gutters-and-margins

https://material-ui.com/customization/breakpoints/

https://material-ui.com/components/hidden/

https://material-ui.com/system/spacing/

`<Container>` help to dynamically change the margin right and left to fit the screen size. 
```jsx harmony
<Container>
  <Grid container>
    <Grid xs={12} md={6} lg={5} item className="landing-grow">    
    </Grid>
    <Grid xs={12} md={6} lg={7} item className="landing-grow-image">
    </Grid>
  </Grid>
</Container>
```

## Hidden something
```jsx harmony
<Hidden smDown>
  <Box />
</Hidden>
```

```jsx harmony
<Grid item xs={false}>
</Grid>
```
