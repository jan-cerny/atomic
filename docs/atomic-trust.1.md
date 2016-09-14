% ATOMIC(1) Atomic Man Pages
% Aaron Weitekamp
% September 2016
# NAME
atomic-trust - Manage system container trust policy


# SYNOPSIS
**atomic trust add|remove**
[**-h**|**--help**]

[**--pubkeys** KEY1 [KEY2][...]]
[**--keytype** GPGKeys]
[**--type** signedBy|insecureAcceptAnything|reject]
[**--sigstore** https://URL[:PORT][/PATH]|file:///PATH]
[**--sigstoretype** docker|atomic|dir]
REGISTRY[/REPOSITORY]

# DESCRIPTION
**atomic trust** manages the trust policy of the host system. Trust policy describes
a registry scope (registry and/or repository) that must be signed by public keys. Trust
is defined in **/etc/containers/policy.json**. Trust is enforced when a user attempts to pull
an image from a registry.

Trust scope is evaluated by most specific to least specific. In other words, policy may
be defined for an entire registry, but refined for a particular repository in that
registry. See below for examples.

Trust **type** provides a way to whitelist ("insecureAcceptAnything") or blacklist
("reject") registries.

Signature servers, or **sigstores**, configure where image signatures are served
for a particular registry scope. This cofiguration is a flat list of
arbitrarily named YAML files in **/etc/containers/registries.d/**. Filenames must end
in **.yaml**. A sigstore may be either an absolute path to a local directory (file:///PATH)
or a remote web server (https://URL).

Trust may be updated using the command **atomic trust add** for an existing trust scope.

# OPTIONS
**-h** **--help**
  Print usage statement.

**--pubkeys**
  A space-separated list of absolute paths to installed public keys. Required
  for **signedBy** type. Default: signedBy

**--keytype**
  The public key type. Default: GPGKeys (only supported value)

**--type**
  The trust type for this policy entry. Accepted values:
    **signedBy** (default): Require signatures with corresponding list of
                            public keys
    **insecureAcceptAnything**: do not require any signatures for this
                                registry scope
    **reject**: do not accept images for this registry scope

**--sigstore**
  A path or remote URL where signatures are found. Prefix filesystem path with
  **file:///PATH** and remote web server with **https://URL[:PORT][/PATH/TO/SIGNATURES]**.

**--sigstoretype**
  Type of signature transport. Accepted values:
    **docker** (default): remote web server
    **atomic**: OpenShift-based Atomic Registry API
    **dir**: Local filesystem


# EXAMPLES
Add public key trust to specific registry repository

    atomic trust add \
           --pubkeys /etc/pki/containers/foo@example.com \
           --sigstore https://s3.bucket/foobar/sigstore/ \
           docker.io/foobar

Modify a trust scope, adding a second public key and changing
the sigstore web server

    atomic trust add \
           --pubkeys /etc/pki/containers/foo@example.com \
                     /etc/pki/containers/bar@example.com \
           --sigstore https://server.example.com/foobar/sigstore/ \
           docker.io/foobar

Accept all unsigned images from a registry

    atomic trust add --type insecureAcceptAnything docker.io

Remove a trust scope

    atomic trust remove docker.io

# HISTORY
September 2016, originally compiled by Aaron Weitekamp (aweiteka at redhat dot com)