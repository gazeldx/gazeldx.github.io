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

## Grid
* When you want to put something on the left, but something on the right, you can use:
```jsx harmony
<Grid container justify="space-between" spacing={1}>
  <Grid item>
  </Grid>
  <Grid item>
  </Grid>
</Grid>
```

* Look at this code:
```jsx harmony
<Grid container>
  {/* Grid one */}
  <Grid container item xs={12} md={8}>
    <Grid>1</Grid>
    <Grid>2</Grid>
  </Grid>
  
  {/* Grid two */}
  {/* Here whether has a `container` prop do matter. If no `container` prop, it's children will not align the previous Grid (Grid one). */}
  <Grid item xs={12} md={4}>
    <Grid>1</Grid>
    <Grid>2</Grid>
  </Grid>
</Grid>
```

# Test
* Test `withStyles`.

```jsx harmony
import { createShallow } from '@material-ui/core/test-utils';

describe('<Issues />', () => {
  let shallow;

  beforeAll(() => {
    shallow = createShallow({ dive: true });
  });
  
  it('something', async () => {
    mockListIssues(issuesRes);

    const wrapper = shallow(
      <ComponentToTest />,
    );
    
    await waitForAsync();

    chai.expect(wrapper.find(SomeInnerComponent).prop('issues')).to.have.lengthOf(1);
  });

})
```
