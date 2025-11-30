# How to Consume Configuration

Where we have configuration parameters, they should be accessible via:

* Command Line Flag
* Environment Variable (with a prefix)
* File (see [How to Store Configuration Files](./how-to-store-configuration-files.md))

The library [spf13/viper] does this especially well. A concrete example might be for an API key for Slack ("abcde"), and the application
might be called "Announce". The configuration should be suppliable via environment variable:

```bash
ANNOUNCE_SLACK_API_KEY="abcde" ./announce
```

Command line flag:

```bash
# Use dot notation to match what might exist in a file.
./announce --slack.api.key="abcde"
```

And File:

```yaml
# Prefer child objects rather than using a underscore to specify configuration values.
slack:
  api:
    key: abcde
```

[spf13/viper]: https://github.com/spf13/viper
