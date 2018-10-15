## Testing

We use `Jest` and `Enzyme` to test our components and functions.

## Table of Contents

1. [Intro](#intro)
1. [Describe](#describe-blocks)
1. [It](#it-blocks)

## Intro

- Testing a components length

```jsx
import React from 'react';
import { shallow } from 'enzyme';
import Component from '../Component';

describe('Component', () => {
  it('is rendered', () => {
    const component = shallow(<Component />);
    expect(component.length).toBe(1);
  });
});
```

- Testing a components prop. Have one it-block for each prop

```jsx
// bad
describe('Component', () => {
  it('passes props', () => {
    const value = 'value';
    const name = 'name';
    const props = { value, name };
    const component = shallow(<Component {...props} />);

    expect(component.find(Person).prop('value')).toBe(value);
    expect(component.find(Person).prop('name')).toBe(name);
  });
});

// good
describe('Component', () => {
  it('passes prop value', () => {
    const value = 'value';
    const component = shallow(<Component value={value} />);

    expect(component.find(Person).prop('value')).toBe(value);
  });
  
  it('passes prop name', () => {
    const name = 'name';
    const component = shallow(<Component name={name} />);

    expect(component.find(Person).prop('name')).toBe(name);
  });
});
```

- Testing style (`css`) use snapshot. Only test styleing with snapshot. Preferably put it at the top of the test file. 
```jsx
  it('renders correctly', () => {
    const component = shallow(<Component />);
    expect(component).toMatchSnapshot();
  });
```

- Mocking a function
```jsx
import * as utils from '../utils/getFullname';

  it('passes prop name', () => {
    const firstName = 'firstName';
    const lastName = 'lastName';
    const name = 'name';

    utils.getFullname = jest.fn().mockReturnValue(name);
    // or
    jest.spyOn('utils', getFullName).mockImplementation(() => name);
    
    const component = shallow(<Component firstName={firstName} lastName={lastName} />);
    
    expect(utils.getName).toHaveBeenCalledWith(firstName, lastName);
    expect(component.find('p').text()).toBe(name);
  });
```

## Describe-blocks

- How to write a describes, our way. Let's say you have this case; if `age` prop is true, render `Adult` component, otherwise render `Child`

```jsx
import React from 'react';
import { shallow } from 'enzyme';
import Adult from '../Adult';
import Child from '../Child';
import Component from '../Component';

describe('Component', () => {
  describe('when age is provided', () => {
    it('renders Adult', () => {
      const age = 'age';
      const component = shallow(<Component age={age} />);

      expect(component.find(Adult).length).toBe(1);
      expect(component.find(Child).length).toBe(0);
    });
  });

  describe('when age is NOT provided', () => {
    it('does NOT render Adult', () => {
      const component = shallow(<Component />);

      expect(component.find(Adult).length).toBe(0);
      expect(component.find(Child).length).toBe(1);
    });
  });
});
```

## It-blocks

- When writing your it-blocks there is no need to specify the word "it" in the description

```jsx
// bad
it('it is rendered', () => {
  // test stuff
});

// good
it('is rendered', () => {
  // test stuff
});
```
