---
title: Custom tips (experimental)
---

Custom Tips allow you to annotate Rush's console messages with custom advice that
is specific to your monorepo.

For example, suppose that your company uses a private NPM registry, which periodically syncs
the latest versions from `npmjs.com`. Suppose that sometimes users try to install a version
that was just published, and has not synced yet. When running `rush update`, they may see
this error:

```
Progress: resolved 0, reused 1, downloaded 0, added 0
/users/example/code/my-repo/apps/my-app:
 ERR_PNPM_NO_MATCHING_VERSION  No matching version found for example-library@1.2.3

This error happened while installing a direct dependency of my-app

The latest release of example-library is "1.1.0".
```

This error is a bit confusing, since the latest release really is `1.2.3`, whereas
the error is referring to the latest version synced by the private registry.
As maintainers of the monorepo, we can reduce tickets from our engineers by printing
a custom tip with better guidance.

The `ERR_PNPM_NO_MATCHING_VERSION` code above is from PNPM. It corresponds to Rush's
tip ID `TIP_PNPM_NO_MATCHING_VERSION`. We can define the tip as follows:

(If you don't have this file, you can generate it using `rush init`.)

**common/config/rush/custom-tips.json**

```js
/**
 * This configuration file allows repo maintainers to configure extra details to be
 * printed alongside certain Rush messages.  More documentation is available on the
 * Rush website: https://rushjs.io
 */
{
  "$schema": "https://developer.microsoft.com/json-schemas/rush/v5/custom-tips.schema.json",

  /**
   * Specifies the custom tips to be displayed by Rush.
   */
  "customTips": [
    // {
    //   /**
    //    * (REQUIRED) An identifier indicating a message that may be printed by Rush.
    //    * If that message is printed, then this custom tip will be shown.
    //    * Consult the Rush documentation for the current list of possible identifiers.
    //    */
    //   "tipId": "TIP_RUSH_INCONSISTENT_VERSIONS",
    //
    //   /**
    //    * (REQUIRED) The message text to be displayed for this tip.
    //    */
    //   "message": "For additional troubleshooting information, refer this wiki article:\n\nhttps://intranet.contoso.com/docs/pnpm-mismatch"
    // }
    {
      "tipId": "TIP_PNPM_NO_MATCHING_VERSION",
       "message": "This \"no matching version\" error from PNPM often results from a new version that has not been synced yet to our company's internal NPM registry.\n\nFor instructions about how to handle that, consult our team wiki:\n\nhttps://example.com/wiki/npm-syncing"
    }
  ]
}
```

With this change, users will now see the custom message alongside the original error:

```
Progress: resolved 0, reused 1, downloaded 0, added 0
/users/example/code/my-repo/apps/my-app:
 ERR_PNPM_NO_MATCHING_VERSION  No matching version found for example-library@1.2.3

This error happened while installing a direct dependency of my-app

The latest release of example-library is "1.1.0".

| Custom Tip (TIP_PNPM_NO_MATCHING_VERSION)
|
| This "no matching version" error from PNPM often results from a new version that has not been synced
| yet to our company's internal NPM registry.
|
| For instructions about how to handle that, consult our team wiki:
|
| https://example.com/wiki/npm-syncing
```

Note that Rush formats custom tips with a `|` prefix to distinguish them from the official messaging from the Rush software.

## Custom Tip identifier

<!-- NOTE: Sort them alphabetically! -->

### TIP_PNPM_INVALID_NODE_VERSION

### TIP_PNPM_MISMATCHED_RELEASE_CHANNEL

### TIP_PNPM_NO_MATCHING_VERSION

### TIP_PNPM_NO_MATCHING_VERSION_INSIDE_WORKSPACE

### TIP_PNPM_OUTDATED_LOCKFILE

### TIP_PNPM_PEER_DEP_ISSUES

### TIP_PNPM_TARBALL_INTEGRITY

### TIP_PNPM_UNEXPECTED_STORE

### TIP_RUSH_INCONSISTENT_VERSIONS

This message is printed by `rush install` or `rush update`
when `ensureConsistentVersions` is enabled in **rush.json**
and some projects have inconsistent dependency versions.

**Example Rush output:**

```
Found 5 mis-matching dependencies!
```
