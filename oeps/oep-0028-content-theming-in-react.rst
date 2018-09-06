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
| Last Modified   | 2018-08-24                                                     |
+-----------------+----------------------------------------------------------------+
| Authors         | Braden MacDonald <braden@opencraft.com>,                       |
|                 | Taranjeet Singh <taranjeet@opencraft.com>                      |
+-----------------+----------------------------------------------------------------+
| Arbiter         |                                                                |
+-----------------+----------------------------------------------------------------+
| Status          | Draft                                                          |
+-----------------+----------------------------------------------------------------+
| Type            | Architecture                                                   |
+-----------------+----------------------------------------------------------------+
| Created         | 2018-08-17                                                     |
+-----------------+----------------------------------------------------------------+
| `Review Period` | 2018-08-17 - 2018-09-02                                        |
+-----------------+----------------------------------------------------------------+

Abstract
-------

OEP-0023 [1] addresses the styling part of a theming system based on React. This proposal adds guidelines for content modification to the React based theming system.

Context
-------

Open edX instances frequently wish to customize not only the colors and styles of the Open edX UI, but also to add, remove, and modify significant parts of the UI to suit their use cases and branding. Doing this in a way that is understandable, clean, and easy to maintain can be a challenge unless the original UI is designed with this in mind.

Another pain of theming systems is providing access to unrelated data to any component. It becomes important to make commonly-used data globally available to all components.

Decision
--------

* We will build two types of components:

  1. Customizable components which will be composed of others components

  2. Internal or non-customizable components which will contain HTML and certain logic

* We will build small modular components which will be non-customizable, stateless and contain HTML, and certain logic. We can see an example of a smaller component ``SiteLogo``, which will later be used in a customizable component ``_Header``.

.. code-block:: js

    const SiteLogo = (props) => {
        return (
            <img src={props.url} alt={props.altText} />
        );
    }

* We will build the complete UI out of the above small modular components. We will aim/prefer to use composition whenever possible. These customizable components will contain little or no HTML nor logic. We can see an example of a customizable component called the ``_Header`` component, which is formed by the composition of ``SiteLogo``, ``MainNav`` and ``UserAvatar`` component.

.. code-block:: js

    class _Header extends React.PureComponent {
        render() {
            return (
                <header>
                    {this.props.logo ? this.props.logo: <SiteLogo/>}
                    <MainNav/>
                    <UserAvatar/>
                </header>
            );
        }
    }

* When creating any complex UI Component we will try to break it down into the smallest reusable components, and build the component out of these small reusable pieces via composition/inheritance. We can see this through an example where we have a theme, which has several components for a default theme.

.. code-block:: js

    // Customizable Header
    class _Header extends React.PureComponent {
        render() {
            return (
                <header>
                    {this.props.logo ? this.props.logo: <SiteLogo/>}
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

Now if we want to customize our ``_Header`` component, and use ``MyCustomAnimatedLogoWidget`` instead of ``SiteLogo``, we can do it as

.. code-block:: js

    const MyThemedHeader = (props) => {
        return (<Header props={<MyCustomAnimatedLogoWidget/>} />)
    }

    // Custom theme:
    export const Header = MyThemedHeader;


* We will provide support via props to control parts of the component when composing components. An example of this can be ``Button`` element

.. code-block:: js

    class Button extends React.PureComponent {
        render() {
            return <button
                color={this.props.color}
                size={this.props.size}
                disable={this.props.isDisable}
                onClick={this.props.onClickHandler}
            />
        }
    }

* We will use methods and placeholders to add additional content to customizable components when using inheritance. These methods will be overridden from subclasses and will be clearly marked as part of the Theme API. We will announce breaking changes if there are any changes to these methods. We can take an example of the above ``DefaultTheme`` and see ``_MainNav`` where it has support to add additional nav links by overriding ``extraNavLinks`` function.

.. code-block:: js

    // Customizable Main Navigation Area
    class MyThemedNav extends _MainNav {
        get extraNavLinks() {
            return (
                <React.Fragment>
                    <a href="/about">About Us</a>
                </React.Fragment>
            );
        }
    }

    // Custom theme:
    export const MainNav = MyThemedNav;
    export const MainNavWrapper = _MainNavWrapper;

* We will generally prefer composition when extending components, however, there can be certain scenarios, under which inheritance is the much better alternative.

1. Inheritance becomes necessary when you want to customize a component, but there is no support via props. An example of this can be ``SiteLogoCircle`` component, where the user wants to a Trademark logo next to the image. Since ``SiteLogoCircle`` does not contain support for trademark logo, it needs to extend via inheritance as follows

.. code-block:: js

    class SiteLogoCircle extends React.PureComponent {
        render() {
            <CircleLogo>
                <Image src={this.props.image} />
            </CircleLogo>
        }
    }

    // if props were present
    class SiteLogoCircle extends React.PureComponent {
        render() {
            <CircleLogo>
                <Image src={this.props.image} />
                {this.props.trademark ? <TradeMarkLogo />:  null}
            </CircleLogo>
        }
    }

    // via inheritance
    class SiteLogoCircleWithTitle extends SiteLogo {
        render() {
            <CircleLogo>
                <Image src={this.props.image} />
                <TradeMarkLogo />
            </CircleLogo>
        }
    }

2. Inheritance becomes necessary when the original author is not aware of the customizations a component can have. An example of this can be Card component, where the author has a CardColor component which gives a background color to the upper half portion of the Card. Now in the custom theme, we want to show an image instead of the background color. This can be done via inheritance

.. code-block:: js

    class Card extends React.PureComponent {

        viewCardDetail(courseId) {
            // open course detail page
        }

        render() {
            <Card>
                <CardColor color={this.props.color} />
                <CardBody>
                    <CardTitle />
                    <CardText />
                    <Button onClick={this.viewCardDetail(this.props.courseId) />}
                </CardBody>
            </Card>
        }
    }

    // overridden version
    class ImageCard extends Card {
        render() {
            <Card>
                <CardImage image={this.props.imageUrl} />
                <CardBody>
                    <CardTitle />
                    <CardText />
                    <Button onClick={this.viewCardDetail(this.props.courseId) />}
                </CardBody>
            </Card>
        }
    }

3. Inheritance often becomes necessary and useful, when we want to override the rendering functionality of any component, and still maintaining access to lifecycle code. Overriding functionality can include removing, re-ordering, replacing or inserting children. An example of this can be Navbar, where the default Navbar has a SearchForm which is left aligned. This Navbar component can now be inherited to place SearchForm as right aligned.

.. code-block:: js

    class Navbar extends React.PureComponent {

        handleSubmit() {
            // handle form submit here
        }

        render () {
            <Nav>
                <NavbarLeft>
                    <SiteTitle />
                    <SearchForm onSubmit={this.handleSubmit}/>
                </NavbarLeft>
                <NavbarRight>
                    <UserNav />
                </NavbarRight>
            </Nav>
        }
    }

    // override Navbar via inheritance
    class CustomNavbar extends Navbar {

        render () {
            <Nav>
                <NavbarLeft>
                    <SiteTitle />
                </NavbarLeft>
                <NavbarRight>
                    <SearchForm onSubmit={this.handleSubmit}/>
                    <UserNav />
                </NavbarRight>
            </Nav>
        }
    }

* Each frontend (e.g. the LMS, os Studio) will have a global redux store that acts as a central place to hold the state of its UI.

* We will consider the layout of the data in the redux store specific to each frontend (LMS, Studio, ecommerce, etc.) as a stable API. We will provide support to pre-fill the store with some common data like current user, current course, list of courses enrolled, etc. We will provide the flexibility for themes to fetch data that are not part of the redux store from REST API's using custom redux actions and store it in their own separate redux store. We will announce breaking changes if the layout of the data changes in global store.

* Wherever we are developing a component that needs to use data from the redux store we will never do so directly in the component implementation. A separate component should be created that will be solely responsible for accessing the data from the store and passing it to the component via props. In React parlance such a component is called a "Container" [2] component, and this term will be used henceforth in the OEP. A container is a react component that has a direct connection to the state managed by redux and access data from the state via mapStateToProps. This way we can keep both non redux connected version as well as the redux connected version of the same component.

* We will have support to compose any component into a container if it needs to access any data from the redux store, which it currently does not have access to. We can see this by an example where ``NavbarHeader`` component initially displays site title. This component now needs to display authenticated username, which is there in the redux store.

.. code-block:: js

    // NavbarHeader component
    class NavbarHeader extends React.Component {
        render() {
            return (
                <h1>{props.title}</h1>
            );
        }
    }

    class NavbarHeaderWithUserName extends NavbarHeader {
        render() {
            return (
                <React.Fragment>
                    <h1>{props.title}</h1>
                    <h3>{props.username}</h3>
                </React.Fragment>
            );
        }
    }

    // NavbarHeader container
    function mapStateToProps(state) {
        return {
            title: state.title,
            username: state.username
        }
    }

    const NavbarHeaderContainer = connect(mapStateToProps, null)(NavbarHeaderWithUserName);

    // use NavbarHeaderContainer instead of NavbarHeaderWithUserName as it has access to the username

Consequences
------------

Theming system becomes more robust to content modification. Any data be it static or dynamic can be easily added to an existing component. It also provides support to request any unrelated data from the global store, thereby giving better customization for a new theme.

However, there will be cases when a component becomes too complex to use which will create the need to rewrite that component as a composition of smaller components.

References
----------

1. OEP-0023 Style Customization
      https://open-edx-proposals.readthedocs.io/en/latest/oep-0023-style-customization.html

2. Container Components
      https://redux.js.org/basics/usagewithreact#presentational-and-container-components
