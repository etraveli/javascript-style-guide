## Testing

We use `Jest` and `Enzyme` to test our components and functions.

## Table of Contents

1. [Intro](#intro)
1. [Describe](#describe-blocks)
1. [It](#it-blocks)
1. [Naming](#naming)

## Intro

- Testing a components prop. _Have one prop test per it-block. Do not group prop test._

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


- Skip the `length` test if you are also testing a prop for example
  ```jsx
    // bad
    describe('Component', () => {
      it('is rendered', () => {
        const component = shallow(<Component />);
        expect(component.find(OtherComponent).length).toBe(1);
      });
      
      it('passes value', () => {
        const value = 'value';
        const component = shallow(<Component value={value} />);
        expect(component.find(OtherComponent).prop('value')).toBe(value);
      });
    });
    
    // good
    describe('Component', () => {
      it('passes value', () => {
        const value = 'value';
        const component = shallow(<Component value={value} />);
        expect(component.find(OtherComponent).prop('value')).toBe(value);
      });
    });
  ```


- Testing style (`css`) use snapshot. _Only test styling with snapshot. Preferably put it at the top of the test file._
  ```jsx
    it('renders correctly', () => {
      const component = shallow(<Component />);
      expect(component).toMatchSnapshot();
    });
  ```
  - If styling changes via a prop you should have a snapshot test for that.
  ```jsx
  describe('when value is provided', () => {
    it('renders correctly', () => {
      const value = 'value';
      const component = shallow(<Component value={value} />);
      expect(component).toMatchSnapshot();
    });
  });

  describe('when value is NOT provided', () => {
    it('renders correctly', () => {
      const component = shallow(<Component />);
      expect(component).toMatchSnapshot();
    });
  });
  ```
  
  - Testing a `styled-component` 
  > Don't export the component, access it via a string. We automatically set `displayName` on `styled-components`
  ```jsx
  // bad 
  import { StyledComponent } from './';

  it('renders correctly', () => {
    const component = shallow(<Component />);
    expect(component.find(StyledComponent)).toMatchSnapshot();
  });

  // good
  it('renders correctly', () => {
    const component = shallow(<Component />);
    expect(component.find('StyledComponent')).toMatchSnapshot();
  });
  ```


- Mocking a function
  ```jsx
  import * as utils from '../utils/getFullname';

    it('renders name', () => {
      const firstName = 'firstName';
      const lastName = 'lastName';
      const name = 'name';

      const getFullnameSpy = jest.spyOn(utils, 'getFullName').mockImplementation(() => name);

      const component = shallow(<Component firstName={firstName} lastName={lastName} />);
      
      expect(utils.getName).toHaveBeenCalledWith(firstName, lastName);
      expect(component.find('p').text()).toBe(name);
      getFullnameSpy.mockRestore();
    });
  ```

## Describe-blocks

- How to write a describe block, our way. Let's say you have this case; if `age` prop is true, render `Adult` component, otherwise render `Child`

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

## Naming
- The test file should have the same name as the file it's testing. 
  ```jsx
    // bad 

    // text resolvers
    headerTextResolver.js
    HeaderTextResolver.jsx

    // containers
    headerTextContainer.js
    HeaderTextContainer.test.jsx

    // good    

    // text resolvers
    HeaderTextResolver.jsx
    HeaderTextResolver.test.jsx

    // containers
    HeaderTextContainer.jsx
    HeaderTextContainer.test.jsx

    // components
    Header.jsx
    Heder.test.jsx

    // util functions
    header.js
    header.test.js
  ```