## GitHub - Branch deployments

You'll want to push from a source branch to your target branch:

```sh
# git push origin <source>:<target>
git push origin main:integration
```

If using GitHub Actions, you'll need to ensure that you have checked out the full source history before attempting the push above, otherwise when pushing it will complain that you don't have the full set of commits:

```yml
- uses: actions/checkout@v4
  with:
    fetch-depth: 0
```

And finally, if you want to trigger a different GitHub Actions workflow, that is watching for a particular branch, you will need to use a different set of credentials. The built-in `GITHUB_TOKEN` will not trigger other workflows. See this [documentation](https://docs.github.com/en/actions/using-workflows/triggering-a-workflow#triggering-a-workflow-from-a-workflow) and this [StackOverflow issue](https://stackoverflow.com/a/64078507). 

Create a new "machine user" on GitHub, grant it access to your repository with `write` permissions, then also create a Personal Access Token for this user, with `repo` scope. Now with the generated token, add as a Secret to Actions, and reference in the `checkout` step:

```yml
- uses: actions/checkout@v4
  with:
    fetch-depth: 0
    token: ${{ secrets.MACHINE_USER_GH_TOKEN }}
```

Any subsequent Git commands will use the same Git credentials that were setup at the `checkout` action.
