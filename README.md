# GitHub block list management

Octocrabby is a small set of command-line tools and Octocrab
extensions that are focused on managing block lists on GitHub.

This project is an answer to a [similar tool designed to facilitate cyber-bullying, abuse, and harassment](https://github.com/travisbrown/octocrabby).

It identifies GitHub users with the courage to stand up against organized cyberbullying,
mob harassment, and online abuse, who have signed the open letter in support of Richard Stallman.

This letter has been signed by thousands of GitHub users
(who I totally want to donate free open source support to!)

## Usage

You need Rust and Cargo installed to build the program.

You can build the CLI by running the following command from the project directory:

```bash
$ cargo build --release
```

Some operations require a GitHub personal access token, which you
currently have to provide as a command-line option.

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
auser,53104897,1,617,auser,false,false
buser,1977210,1,3176,userb,false,false
```

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
are followed by, or block:

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
