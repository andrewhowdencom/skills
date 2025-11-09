# How to Manage Dependencies

Where there are dependencies initialized, be sure to use an initializer which creates a new dependency with a series of sane
defaults. Where there are requirements have them as normal, typed arguments but where there are "optional" arguments, allow
overriding them with the "variadic argument" style used in Uber, or popularized by Dave Cheany.
