## Emotion

We use `Emotion` to go about our styleing 
https://github.com/emotion-js/emotion

## Table of Contents

1. [Intro](#intro)
1. [Naming Conventions](#naming-conventions)

## Intro

  - Styled components

  ```jsx
  const Link = styled('a')`
    color: red;
  `;

  const Component = ({ text, url }) => (
    <Link href={url}>{text}</Link>
  );
  ```
  - css

  ```jsx
  const linkStyles = css`
    color: red;
  `;

   const Component = ({ text, url }) => (
    <a className={linkStyles} href={url}>{text}</a>
  );
  ```

## Naming Conventions
  - Styled component
    * _Avoid naming such as `Container`, as it collides with our `redux` and `graphql` `containers`_
  ```jsx
    // bad
    const Container = styled('div')`
      padding: 1rem;
    `;

    const Component = ({ text }) => (
      <Container>{text}</Container>
    );

    // good
    const Wrapper = styled('div')`
      padding: 1rem;
    `;

    const Component = ({ text }) => (
      <Wrapper>{text}</Wrapper>
    );

  ```
  - css
  ```jsx
    // bad
    const paragraph = css`
      padding: 1rem;
    `;

    const Component = ({ text }) => (
      <p className={paragraph}>{text}</p>
    );

    // good
    const paragraphStyles = css`
      padding: 1rem;
    `;

    const Component = ({ text }) => (
      <p className={paragraphStyles}>{text}</p>
    );

  ```