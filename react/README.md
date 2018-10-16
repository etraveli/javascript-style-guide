# Etraveli React/JSX Style Guide

This is how we write React.

## Table of Contents

1. [Basic Rules](#basic-rules)
1. [Class](#class)
1. [Stateless](#stateless)
1. [Destructuring](#destructuring)
1. [Naming](#naming)
1. [Props](#props)
1. [Refs](#refs)
1. [Parentheses](#parentheses)
1. [Tags](#tags)
1. [Methods](#methods)
1. [PropTypes](#prop-types)

## Basic Rules

- Only include one React component per file.
  - However, multiple [Stateless, or Pure, Components](https://facebook.github.io/react/docs/reusable-components.html#stateless-functions) are allowed per file. eslint: [`react/no-multi-comp`](https://github.com/yannickcr/eslint-plugin-react/blob/master/docs/rules/no-multi-comp.md#ignorestateless).
- Always use JSX syntax.
- Do not use `React.createElement` unless you’re initializing the app from a file that is not JSX.

## Class

- When using Class component import `Component` directly
    > Why? It improves readability

  ```jsx
  // bad
  import React from 'react';

  class User extends React.Component {
    // ...
  }

  // good
  import React, { Component } from 'react';

  class User extends Component {
    // ...
  }
  ```

- When using internal state without props reference, don't use a constructor
    > Why? Less code is better.

  ```jsx
  // bad
  class User extends Component {
    constructor(props) {
      super(props);
      this.state = { isOpen: false };
    }
  }

  // good
  class User extends Component {
    constructor(props) {
      super(props);
      this.state = { isOpen: props.value };
    }
  }

  // good
  class User extends Component {
    state = { isOpen: false };
  }
  ```

## Stateless

- When not using internal state, use stateless component
  > Why? We want to keep as functional as possible
  ```jsx
  // bad
  class User extends Component {
    render() {
      return <div>{this.props.name}</div>;
    }
  }

  // good
  const User = ({ name }) => <div>{name}</div>;
  ```

## Destructuring
 - Always destruct when possible
    > Why? It improves readability

    ```jsx
    // bad
    class User extends Component {
      render() {
        return <div>{this.props.name}</div>;
      }
    }

    // good
    class User extends Component {
      render() {
        const { name } = this.props;
        return <div>{name}</div>;
      }
    }

    // bad
    const User = props => <div>{props.name}</div>;

    // bad
    const User = props => {
      const { name } = props;
      return <div>{props.name}</div>;
    };

    // good
    const User = ({ name }) => <div>{name}</div>;
    ```

## Naming

- **Extensions**: Use `.jsx` extension for React components. eslint: [`react/jsx-filename-extension`](https://github.com/yannickcr/eslint-plugin-react/blob/master/docs/rules/jsx-filename-extension.md)
- **Filename**: Use PascalCase for filenames. E.g., `ReservationCard.jsx`.
- **Reference Naming**: Use PascalCase for React components and camelCase for their instances. eslint: [`react/jsx-pascal-case`](https://github.com/yannickcr/eslint-plugin-react/blob/master/docs/rules/jsx-pascal-case.md)

  ```jsx
  // bad
  import reservationCard from './ReservationCard';

  // good
  import ReservationCard from './ReservationCard';

  // bad
  const ReservationItem = <ReservationCard />;

  // good
  const reservationItem = <ReservationCard />;
  ```

- **Component Naming**: Use the filename as the component name. For example, `ReservationCard.jsx` should have a reference name of `ReservationCard`. However, for root components of a directory, use `index.jsx` as the filename and use the directory name as the component name:

  ```jsx
  // bad
  import Footer from './Footer/Footer';

  // bad
  import Footer from './Footer/index';

  // good
  import Footer from './Footer';
  ```

   - Keep "context" in file names, favouring `SeatMapHeader` over `Header`, but drop it in imports
    ```jsx
    // bad
    import SeatMapHeader from './SeatMapHeader';

    // good
    import Header from './SeatMapHeader';  
    ```

- **Containers and text-resolvers**: When used in render method, leave out the suffix.

  ```jsx
  // file 1 contents
  const CheckBoxContainer = () => {
    // ...
  };
  // file 2 contents
  const DropdownTextResolver = () => {
    // ...
  };
  // bad
  import CheckBoxContainer from '../containers/CheckBoxContainer';
  import DropdownTextResolver from '../text-resolvers/DropdownTextResolver';

  // good
  import CheckBox from '../containers/CheckBoxContainer';
  import Dropdown from '../text-resolvers/DropdownTextResolver';
  ```

- **Higher-order Component Naming**: When possible use the prefix `with...` e.g. `WithLoading`


    ```jsx

    // bad
    const LoadingHOC = WrappedComponent => props => (
      <Loading>
        <WrappedComponent {...props} />
      </Loading>
    );


    // good
    const WithLoading = WrappedComponent => props => (
      <Loading>
        <WrappedComponent {...props} />
      </Loading>
    );
    ```

## Props

- Always use camelCase for prop names.

  ```jsx
  // bad
  <Foo
    UserName="hello"
    phone_number={12345678}
  />

  // good
  <Foo
    userName="hello"
    phoneNumber={12345678}
  />
  ```

- Omit the value of the prop when it is explicitly `true`. eslint: [`react/jsx-boolean-value`](https://github.com/yannickcr/eslint-plugin-react/blob/master/docs/rules/jsx-boolean-value.md)

  ```jsx
  // bad
  <Foo hidden={true} />

  // good
  <Foo hidden />
  ```

- Always include an `alt` prop on `<img>` tags. If the image is presentational, `alt` can be an empty string or the `<img>` must have `role="presentation"`. eslint: [`jsx-a11y/alt-text`](https://github.com/evcohen/eslint-plugin-jsx-a11y/blob/master/docs/rules/alt-text.md)

  ```jsx
  // bad
  <img src="hello.jpg" />

  // good
  <img src="hello.jpg" alt="Me waving hello" />

  // good
  <img src="hello.jpg" alt="" />

  // good
  <img src="hello.jpg" role="presentation" />
  ```

- Do not use words like "image", "photo", or "picture" in `<img>` `alt` props. eslint: [`jsx-a11y/img-redundant-alt`](https://github.com/evcohen/eslint-plugin-jsx-a11y/blob/master/docs/rules/img-redundant-alt.md)

  > Why? Screenreaders already announce `img` elements as images, so there is no need to include this information in the alt text.

  ```jsx
  // bad
  <img src="hello.jpg" alt="Picture of me waving hello" />

  // good
  <img src="hello.jpg" alt="Me waving hello" />
  ```

- Use only valid, non-abstract [ARIA roles](https://www.w3.org/TR/wai-aria/#usage_intro). eslint: [`jsx-a11y/aria-role`](https://github.com/evcohen/eslint-plugin-jsx-a11y/blob/master/docs/rules/aria-role.md)

  ```jsx
    // bad - not an ARIA role
    <div role="datepicker" />

    // bad - abstract ARIA role
    <div role="range" />

    // good
    <div role="button" />
  ```

- Do not use `accessKey` on elements. eslint: [`jsx-a11y/no-access-key`](https://github.com/evcohen/eslint-plugin-jsx-a11y/blob/master/docs/rules/no-access-key.md)

  > Why? Inconsistencies between keyboard shortcuts and keyboard commands used by people using screenreaders and keyboards complicate accessibility.

    ```jsx
      // bad
      <div accessKey="h" />

      // good
      <div />
    ```

- Avoid using an array index as `key` prop, prefer a stable ID. eslint: [`react/no-array-index-key`](https://github.com/yannickcr/eslint-plugin-react/blob/master/docs/rules/no-array-index-key.md)

  > Why? Not using a stable ID [is an anti-pattern](https://medium.com/@robinpokorny/index-as-a-key-is-an-anti-pattern-e0349aece318) because it can negatively impact performance and cause issues with component state.

    We don’t recommend using indexes for keys if the order of items may change.

     ```jsx
      // bad
      {
        todos.map((todo, index) => <Todo {...todo} key={index} />);
      }
      // good
      {
        todos.map(todo => <Todo {...todo} key={todo.id} />);
      }
    ```

- Use spread props sparingly.
    > Why? Otherwise you’re more likely to pass unnecessary props down to components. And for React v15.6.1 and older, you could [pass invalid HTML attributes to the DOM](https://reactjs.org/blog/2017/09/08/dom-attributes-in-react-16.html).

    ```jsx
      // bad
      const User = ({ name, ...rest }) => <Name {..rest}>{name}</Name>

      // good
      const User = ({ name, number }) => <Name number={number}>{name}</Name>
    ```

 - Use alphabetic order
    > Why? It improves readability
  
    ```jsx
      // bad 
      const User = ({ name, age, info }) => (
        // do stuff
      );
      
      // good 
      const User = ({ age, info, name }) => (
        // do stuff
      );

    ```

## Methods
  - Do not bind methods, use arrow functions
    > Why? Less lines of codes and follow our convention with arrow functions.

    ```jsx
      // bad
      class extends Component {
        handleClick() {
          // do stuff
        }

        render() {
          return <div onClick={this.handleClick.bind(this)} />;
        }
      }

      // good
      class extends Component {
        handleClick = () => {
          // do stuff
        }

        render() {
          return <div onClick={this.handleClick} />;
        }
      }
    ```
    
  - Do not use underscore prefix for internal methods of a React component.
      > Why? Underscore prefixes are sometimes used as a convention in other languages to denote privacy. But, unlike those languages, there is no native support for privacy in JavaScript, everything is public. Regardless of your intentions, adding underscore prefixes to your properties does not actually make them private, and any property (underscore-prefixed or not) should be treated as being public. See issues [#1024](https://github.com/airbnb/javascript/issues/1024), and [#490](https://github.com/airbnb/javascript/issues/490) for a more in-depth discussion.

      ```jsx
      // bad
      class extends Component {
        _onClickSubmit() {
          // do stuff
        },

        // other stuff
      });

      // good
      class extends Component {
        onClickSubmit() {
          // do stuff
        }

        // other stuff
      }
    ```

## PropTypes

  - How to define `PropTypes` and `defaultProps`

    ```jsx
    import PropTypes from 'prop-types';
    
    const User = ({ age, children, name }) => (
      // do stuff
    )

    User.propTypes = {
      age: PropTypes.number,
      name: PropTypes.string.isRequired,
      children: PropTypes.node,
    }
    
    User.defaultProps = {
      name: 'Ulf',
    }
    ```
