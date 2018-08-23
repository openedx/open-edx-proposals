==================================
OEP-0028: Content Theming in React
==================================

+-----------------+----------------------------------------------------------------+
| OEP             | :doc:`OEP-0028 </oeps/oep-0028-arch-content-theming-react>`    |
|                 |                                                                |
|                 |                                                                |
|                 |                                                                |
|                 |                                                                |
+-----------------+----------------------------------------------------------------+
| Title           | Content Theming in React                                       |
+-----------------+----------------------------------------------------------------+
| Last Modified   | 2018-08-23                                                     |
+-----------------+----------------------------------------------------------------+
| Authors         | Braden MacDonald <braden@opencraft.com>,                       |
|                 | Taranjeet Singh <taranjeet@opencraft.com>                      |
+-----------------+----------------------------------------------------------------+
| Arbiter         | Ari Rizzitano <arizzitano@edx.org>                             |
+-----------------+----------------------------------------------------------------+
| Status          | Draft                                                          |
+-----------------+----------------------------------------------------------------+
| Type            | Architecture                                                   |
+-----------------+----------------------------------------------------------------+
| Created         | 2018-08-17                                                     |
+-----------------+----------------------------------------------------------------+
| `Review Period` | 2018-08-17 - 2018-08-25                                        |
+-----------------+----------------------------------------------------------------+

Abstract
-------

OEP-0023 [1] addresses the styling part of a theming system based on React. This proposal adds guidelines for content modification aspect to the React based theming system

Context
-------

Open edX instances frequently wish to customize not only the colors and styles of the Open edX UI, but also to add, remove, and modify significant parts of the UI to suit their use cases and branding. Doing this in a way that is understandable, clean, and easy to maintain can be a challenge unless the original UI is designed with this in mind.

Another pain of theming system is providing access to unrelated data to any component. This data may not be used by earlier components but is required in a particular theme. It becomes an important concern for writing a theming system where a global access to data to all the components in the UI system should be provided.

Decision
--------

* We will build small modular components which will be non-customizable, stateless and contain HTML, and certain logic. We can see an example of a smaller component SiteLogo, which will later be used in a customizable component _Header.

.. code-block:: js

    const SiteLogo = (props) => {
        return (
            <img src={props.url} alt={props.altText} />
        );
    }

* We will build the complete UI out of the above small modular components. We will generally use composition to build a customizable component. However, inheritance can be used at times. These customizable components will neither contain HTML nor logic. We can see an example of a customizable component called the _Header component, which is formed by the composition of SiteLogo, MainNav and UserAvatar component.

.. code-block:: js

    class _Header extends React.PureComponent {
        render() {
            return (
                <header>
                    <SiteLogo/>
                    <MainNav/>
                    <UserAvatar/>
                </header>
            );
        }
    }

* We will follow a 2 step approach to create any UI Component. We will first break down a complex component into smaller non-customizable components. We will then compose these smaller components to form a complex UI.

We can see this through an example where we have a theme, which has several components for a default theme.

.. code-block:: js

    // Customizable Header
    class _Header extends React.PureComponent {
        render() {
            return (
                <header>
                    <SiteLogo/>
                    <MainNav/>
                    <UserAvatar/>
                </header>
            );
        }
    }

    // Customizable Main Navigation Area
    class _MainNav extends React.PureComponent {
        render() {
            return (
                <MainNavWrapper>
                    <a href="/">Home</a>
                    <LoginLink/>
                    {this.extraNavLinks}
                </MainNavWrapper>
            );
        }
        get extraNavLinks() { return []; }
    }
    // Internal MainNavWrapper - not meant to be modified in most cases
    class _MainNavWrapper extends React.PureComponent {
        render() {
            return (
                <div className="mainNav">
                    <ul>
                        {React.Children.map(this.props.children, (child) => (child ? <li>{child}</li> : null))}
                    </ul>
                </div>
            )
        }
    }

    // Default Theme:
    export const Header = _Header;
    export const MainNav = _MainNav;
    export const MainNavWrapper = _MainNavWrapper;


Now if we want to customize our _Header component, we can easily do it like

.. code-block:: js

    // SiteLogo being updated in Header
    class MyThemedHeader extends _Header {
        render() {
            return (
                <header>
                    {/* Replace <SiteLogo/> with a fancy widget */}
                    <MyCustomAnimatedLogoWidget/>
                    <MainNav/>
                    <UserAvatar/>
                </header>
            );
        }
    }
    // Custom theme:
    export const Header = MyThemedHeader;
    export const MainNavWrapper = _MainNavWrapper;


* We will provide support to add data or other components by using `props.children`. The `props.children` property is provided from React and can be extended to include specialized children like left and right for a Pane component. An example of this can be

.. code-block:: js

    const Header = (props) => {
        return (
            <h1>{props.title}</h1>
            {props.children}
        )
    }

    // use
    <Header title={"Open Edx"} >
        <h4>Open Source MOOC platform</h4>
    </Header>

Another example where specialized children are passed

.. code-block:: js

    const Pane = (props) => {
        return (
            <div className="pane">
                <div className="pane-left">
                    {props.left}
                </div>
                <div className="pane-right">
                    {props.right}
                </div>
            </div>

        )
    }

    // use
    <Pane left={<Image />} right={<Explanation />} />


* We will use function and placeholder to add additional content in customizable components. We can take an example of the above DefaultTheme and see _MainNav where it has support to add additional nav links by overriding `extraNavLinks` function.

.. code-block:: js

    // Customizable Main Navigation Area
    class MyThemedNav extends _MainNav {
        get extraNavLinks() {
            return [
                <a href="/about">About Us</a>,
            ];
        }
    }

    // Custom theme:
    export const MainNav = MyThemedNav;
    export const MainNavWrapper = _MainNavWrapper;


* We will provide support for a global redux store, which will act as a central place to store data. This store will access to all data available to the system.

* We will consider the layout of the data in the global store as an API. We will provide support to pre-fill the store with some common data like current user, current course, list of courses enrolled, etc. and add the less commonly needed data from REST API's using built-in redux actions. We will announce breaking changes if the layout of the data changes in store.

* We will use containers [2] to access data from the redux store and provide it to components via props. A container is a react component that has a direct connection to the state managed by redux and access data from the state via mapStateToProps. We will use Container as a mechanism to separate data access functionality from the Component. This way we can keep both non redux connected version as well as redux connected version of the same component.

* We will have support to convert any component into a container if it needs to access any data from the redux store, which it currently does not have access to. We can see this by an example where NavbarHeader component initially displays site title. This component now needs to display authenticated username, which is there in the redux store.

.. code-block:: js

    // NavbarHeader component
    const NavbarHeaderComponent = (props) => {
        return (
            <h1>{props.title}</h1>
            {props.username}
        )
    }


    // NavbarHeader container

    function mapStateToProps(state) {
        return {
            title: state.title,
            username: state.username
        }
    }

    const NavbarHeaderContainer = connect(mapStateToProps, null)(NavbarHeaderComponent);

    // use NavbarHeaderContainer instead of NavbarComponent as it has access to the username

Consequences
------------

Theming system becomes more robust to content modification. Any data be it static or dynamic can be easily added to an existing component. It also provides support to request any unrelated data from the global store, thereby giving better customization for a new theme.

However, there will be cases when a component becomes too complete to use which will create the need to rewrite that component as a composition of smaller components.

References
----------

1. OEP-0023 Style Customization
      https://open-edx-proposals.readthedocs.io/en/latest/oep-0023-style-customization.html

2. Container Components
      https://redux.js.org/basics/usagewithreact#presentational-and-container-components
