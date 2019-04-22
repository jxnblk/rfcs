- Start Date: 2019-04-22
- RFC PR: (leave this empty)
- Gatsby Issue: (leave this empty)

# Summary

When developing Gatsby themes, we have to be careful about how CSS is applied to pages, layouts,
and components and be deligent to help ensure that one theme's styles do not cause side effects
in another theme or in the end-user's site itself.
While tools like Emotion and Styled Components help keep styles isolated to the components used in a particular theme,
there are a few CSS properties where controlling styles at a global level is desirable.

This RFC proposes including a bare-minimum CSS reset for any site that uses themes to help ensure that theme authors
do not need to accomodate for differences in how a particular site's styles might affect the styles of the theme.
In particular, this RFC suggests setting `box-sizing: border-box` on a global,
site-wide level to ensure that themes can safely expect styles to behave in a certain manner.


# Basic example

For sites that do not use the `__experimentalThemes` (or `themes`) flag in the Gatsby config, nothing should change.
For sites with themes configured, the following CSS rules would be applied to the site.

```css
*, *::before, *::after {
  box-sizing: border-box
}

body {
  margin: 0
}
```

This proposal intentionally omits styles that can safely be applied at the component level, such as typographic styles for headings, paragraphs, etc.

# Motivation

<!--
Why are we doing this? What use cases does it support? What is the expected
outcome?

Please focus on explaining the motivation so that if this RFC is not accepted,
the motivation could be used to develop alternative solutions. In other words,
enumerate the constraints you are trying to solve without coupling them too
closely to the solution you have in mind.
-->

The default `content-box` value for `box-sizing` is often regarded as one of the ["mistakes" in the design of CSS][mistakes].
The difference between these two values can result in dramatic differences in how padding styles are applied to elements,
and many CSS and component libraries (e.g. [Bootstrap](https://getbootstrap.com)) work under the assumption that `box-sizing: border-box` is set at a global level.
The behavior of `box-sizing: border-box` is generally regarded as a more natural way of thinking about padding and element dimensions
and should help set up theme authors for success in avoiding unintended style bugs.
This should also help ensure that themes are composable and will not render styles differently depending on configuration.

[mistakes]: https://wiki.csswg.org/ideas/mistakes

Related:

- https://github.com/mozdevs/cssremedy
- https://github.com/necolas/normalize.css/
- https://www.paulirish.com/2012/box-sizing-border-box-ftw/


# Detailed design

<!--
This is the bulk of the RFC. Explain the design in enough detail for somebody
familiar with Gatsby to understand, and for somebody familiar with the
implementation to implement. This should get into specifics and corner-cases,
and include examples of how the feature is used. Any new terminology should be
defined here.
-->

- TK: different options for implementation
  - Internal plugin?
  - A base theme that is loaded by default
  - Other?

# Drawbacks

<!--
Why should we *not* do this? Please consider:

- implementation cost, both in term of code size and complexity
- whether the proposed feature can be implemented in user space
- the impact on teaching people Gatsby
- integration of this feature with other existing and planned features
- cost of migrating existing Gatsby applications (is it a breaking change?)

There are tradeoffs to choosing any path. Attempt to identify them here.
-->

- This shouldn't be difficult to implement initially.
- Once this *is* implemented, it creates some amount of lock-in, and making any changes to this could be potentially expensive.
- If this *were* left to user space, different themes could use different styling approaches and result in inconsistent styling when themes are composed
- Some developers might prefer not using `box-sizing: border-box`
- Some third-party libraries might conflict with using this globally, requiring overrides to the `box-sizing` property at a component-level
- Minimal education


# Alternatives

<!--
What other designs have been considered? What is the impact of not doing this?
-->

- Global style component
- Handling `box-sizing: border-box` on a per-component basis
- Using an existing, well-known solution such as Normalize.css
- Use a forked version of Normalize.css or CSS Remedy

# Adoption strategy

<!--
If we implement this proposal, how will existing Gatsby developers adopt it? Is
this a breaking change? Can we write a codemod? Should we coordinate with
other projects or libraries?
-->

- This shouldn't affect Gatsby users who are not using themes
- Authors of existing themes will need to ensure that their styling works with these new styles
  - Adjust width, height, and padding values where needed
  - This would be difficult to codemod
- Sites using existing themes may need to make similar style adjustments
- This will need to be clearly documented in the themes docs

# How we teach this

<!--
What names and terminology work best for these concepts and why? How is this
idea best presented? As a continuation of existing Gatsby patterns?

Would the acceptance of this proposal mean the Gatsby documentation must be
re-organized or altered? Does it change how Gatsby is taught to new developers
at any level?

How should this feature be taught to existing Gatsby developers?
-->

- There are multiple existing resources about writing CSS with `box-sizing: border-box`
- The amount of effort required to teach this should be minimal
- This change should be well-documented in the themes documentation

# Unresolved questions

<!--
Optional, but suggested for first drafts. What parts of the design are still
TBD?
-->
