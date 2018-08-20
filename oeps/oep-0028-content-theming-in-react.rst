==================================
OEP-0028: Content Theming in React
==================================

.. This OEP template is based on Nygard's Architecture Decision Records.

+-----------------+----------------------------------------------------------------+
| OEP             | :doc:`OEP-0028 </oeps/oep-0028-arch-content-theming-react>`    |
|                 |                                                                |
|                 |                                                                |
|                 |                                                                |
|                 |                                                                |
+-----------------+----------------------------------------------------------------+
| Title           | Content Theming in React                                       |
+-----------------+----------------------------------------------------------------+
| Last Modified   | 2018-08-17                                                     |
+-----------------+----------------------------------------------------------------+
| Authors         | Braden MacDonald                                               |
+-----------------+----------------------------------------------------------------+
| Arbiter         |                                                                |
+-----------------+----------------------------------------------------------------+
| Status          | Draft                                                          |
+-----------------+----------------------------------------------------------------+
| Type            | Architecture                                                   |
+-----------------+----------------------------------------------------------------+
| Created         | 2018-08-17                                                     |
+-----------------+----------------------------------------------------------------+
| `Review Period` | 2018-08-17 - 2018-08-25                                        |
+-----------------+----------------------------------------------------------------+

Context
-------

This section describes the forces at play, including technological, political,
social, and project local. These forces are probably in tension, and should
be called out as such. The language in this section is value-neutral. It is
simply describing facts.

Decision
--------

This section describes our response to the forces described in the Context.
It is stated in full sentences, with active voice. "We will ..."

Broad Points
-------------

NOTE: This is just a heading to list down my findings/observations for the proposal.
Once final, this will be removed

From Braden's `proposal<https://gist.github.com/bradenmacdonald/34bf8bfc8f737f9b23a2664091b8f115>_`,
we get the following points which are further elaborated below

1. Build the UI out of small, modular React components as much as possible

A `God component<https://en.wikipedia.org/wiki/God_object>_` tends to know and do everything within the application. No component should be a God component. Each God component should be broken down into smaller components. These small and modular components should follow the Single responsibility principle.

> A component has a single responsibility when it has one reason to change.

The above definition is unambiguous because there can be lots of ways to argue whether a component has more than one responsibility or not but it becomes easier to decide when this is conveyed in terms of change. The change can be cosmetic like color, spacing, animation, etc or functional like adding new feature etc.

Single Responsibility Principle is important because it becomes easy to change the component as it is isolated. It is helpful in the theming architecture where the most common use case would be to override a particular part of the UI which will be a component.

An example of this can be `Navbar` component. The Navbar as a component can be broken down into many small components, like

``
<Navbar>
  <NavbarHeader>
    <NavbarBrand>
      <a href="#">Open Edx OEP</a>
    </NavbarBrand>
  </NavbarHeader>
  <Nav>
    <NavItem href="#1">
      Link 1
    </NavItem>
    <NavItem href="#2">
      Link 2
    </NavItem>
    <NavDropdown title="Dropdown">
      <MenuItem>OEP-1</MenuItem>
      <MenuItem>OEP-2</MenuItem>
      <MenuItem>OEP-3</MenuItem>
      <MenuItemDivider />
      <MenuItem>What is an OEP?</MenuItem>
    </NavDropdown>
  </Nav>
</Navbar>
``

2. Build two types of components: "customizable" ones that only compose others using JSX and contain little-or-no HTML nor logic, as well as "internal" components that contain logic and detailed HTML and CSS, etc.

**Customizable components** consists of smaller specialized components, which may contain HTML, CSS or some logic. These smaller components when glued together through composition forms a customizable component. Below is an example of a customizable component

``
// Customizable Header
class _Header extends React.PureComponent<Props, State> {
    public render() {
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
class _MainNav extends React.PureComponent<Props> {
    public render() {
        return (
            <MainNavWrapper>
                <a href="/">Home</a>
                <LoginLink/>
                {this.extraNavLinks}
            </MainNavWrapper>
        );
    }
    get extraNavLinks(): JSX.Element[] { return []; }
}
// Internal MainNavWrapper - not meant to be modified in most cases
class _MainNavWrapper extends React.PureComponent<Props> {
    public render() {
        return <div className="mainNav">
            <ul>
                {React.Children.map(this.props.children, (child) => (child ? <li>{child}</li> : null))}
            </ul>
        </div>;
    }
}

// Default Theme:
export const Header = _Header;
export const MainNav = _MainNav;
export const MainNavWrapper = _MainNavWrapper;
``

The above is an example of a customizable component and now below is an example of how this can be overridden to create a new custom theme

``
// Customizable Header
class MyThemedHeader extends _Header {
    public render() {
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
// Customizable Main Navigation Area
class MyThemedNav extends _MainNav {
    get extraNavLinks() {
        return [
            <a href="/about">About Us</a>,
        ];
    }
}

// My theme:
export const Header = MyThemedHeader;
export const MainNav = MyThemedNav
export const MainNavWrapper = _MainNavWrapper;

``

**Internal components** are the smaller components which contain html, css or some logic. They follow the Single Responsibility Principle and are passed data through props. We can also use Higher Order Components and proxy props around smaller components to give some additional properties or modify some existing properties. An example of this can be creating MyCustomAnimatedLogoWidget component using SiteLogo component.

``

function customizeSiteLogo(WrappedComponent) {
    return class NewWrappedComponent extends React.Component {
        render() {
            const propsProxy = {
                ...this.props,
                // update existing logo image url
                imageUrl: 'http://www.example.com/newlogo.png',
                altText: 'Animated Version'
            }
            return <WrappedComponent {...propsProxy} />
        }
    }
}

MyCustomAnimateLogoWidget = customizeSiteLogo(SiteLogo);
``


Consequences
------------

This section describes the resulting context, after applying the decision.
All consequences should be listed here, not just the "positive" ones. A particular
decision may have positive, negative, and neutral consequences, but all of them
affect the team and project in the future.

References
----------

List any additional references here that would be useful to the future reader.
See `Documenting Architecture Decisions`_ for further input.

.. _Documenting Architecture Decisions: http://thinkrelevance.com/blog/2011/11/15/documenting-architecture-decisions
