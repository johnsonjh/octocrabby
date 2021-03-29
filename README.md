# GitHub block list management

Octocrabby is a small set of command-line tools and [Octocrab][octocrab]
extensions that are focused on managing block lists on [GitHub][github].

This project is an answer to a [similar tool designed to facilitate cyber-bullying and harassment](https://github.com/travisbrown/octocrabby).

It identifies those with the courage to stand up and fight online
harassment and abuse by signing the [open letter][rms-support-letter]
supporting Richard Stallman. 

This letter has been signed by thousands of GitHub users
(who I totally want to donate free open source support to!)

## Usage

This project is made with [Rust][rust], so you need Rust and [Cargo][cargo]
installed to use it. If you've followed [these instructions][rust-installation]
and cloned this repo locally, you can build the CLI by running the following
command from the project directory:

```bash
$ cargo build --release
```

Most operations require a [GitHub personal access token][github-token], which
you currently have to provide as a command-line option. If you want to use
the mass-blocking functionality, you'll need to select the `user` scope when
creating your token. If you only want to generate reports or export your
follower or block lists, that shouldn't be necessary.

### Contributor reports

One operation that doesn't require a personal access token is
`list-pr-contributors`:

```bash
$ target/release/crabby \
  -vvvv list-pr-contributors \
  -r rms-support-letter/rms-support-letter.github.io
```

If no token is provided, this command will output a CSV document with a
row for each GitHub user who contributed a pull request to the given
repository. Each row will have three columns:

1. GitHub username
2. GitHub user ID
3. Number of PRs for this repository

For example:

```csv
0x0000ff,1977210,1
```

If you provide a personal access token to this command (via `-t`),
the output will include several additional columns:

1. GitHub username
2. GitHub user ID
3. Number of PRs for this repo
4. Number of days between account creation and the first PR to this repo
5. The user's name (if available)
6. A boolean indicating whether you follow this user
7. A boolean indicating whether this user follows you

For example:

```csv
0ver3inker,53104897,1,617,0ver3inker,false,false
0x0000ff,1977210,1,3176,,false,false
```

You can find copies of the output of this command in this project's
[data directory][data-directory].

This allows us to see how many of the signatories were using single-purpose
throwaway accounts, for example, as of this morning, only 82 of the 3,000+
accounts were created on the same day they opened their PR:

```bash
$ awk -F, '$4 == 0' rms-contributors.csv | wc
     82     102    3282
```

Fantastic!

You can also check how many of the signers follow you on GitHub:

```bash
$ egrep -r "true,(true|false)$" rms-contributors.csv | wc
```

And how many you follow:

```bash
$ egrep -r "true$" rms-contributors.csv | wc
```

### Follow and block list export

The CLI also allows you to export lists of users you follow,
are followed by, and block:

```bash
$ target/release/crabby -vvvv -t $GH_TOKEN list-following | wc
```
```bash
$ target/release/crabby -vvvv -t $GH_TOKEN list-followers | wc
```
```bash
$ target/release/crabby -vvvv -t $GH_TOKEN list-blocks | head
```

The format is a two-column CSV with username and user ID.

## License

This project is licensed under the Mozilla Public License, version 2.0.
See the LICENSE file for details.
