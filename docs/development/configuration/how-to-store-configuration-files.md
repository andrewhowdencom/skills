# How to Store Configuration Files

Where applications have configuration, they should store that configuration in a path that follows the XDG standards. The
Golang library from [ardg/xdg] is a particularly excellent implementation finding or writing to those files.

For details on how to load this configuration, see [How to Consume Configuration](./how-to-consume-configuration.md).

[ardg/xdg]: https://github.com/ardg/xdg
