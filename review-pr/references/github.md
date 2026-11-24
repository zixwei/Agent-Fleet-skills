# GitHub review context

Use the available GitHub integration when it can supply these details. The examples use `gh` and a local clone if that is the available route.

- Get the request and revision IDs with `gh pr view <number-or-url> --json number,title,url,state,body,baseRefName,baseRefOid,headRefName,headRefOid`.
- Use `gh pr view --json number,title,url,state` for the current branch or `gh pr list --head <branch> --state open` for a named branch. Use `gh pr list --search '<ticket>' --state all` when the user identifies the change by ticket. Confirm the repository when remotes or matching requests are ambiguous.
- Get conversation context with `gh pr view <number-or-url> --comments`. Use the integration or API for inline threads and their resolution state. Use `gh pr checks <number-or-url>` for reported checks and verify that the results apply to the reviewed head.
- In the local clone, if the objects are missing, fetch the base branch and `refs/pull/<number>/head` from the request's target repository. The pull ref also works for fork heads. Check that the recorded base and head objects are available locally before using them. If the refs moved during resolution, refresh the metadata or identify the exact revision you could review. Use the pinned revisions for file lists, diffs, and source reads.
- Cite source at the reviewed SHA with `<repository-url>/blob/<sha>/<path>#L<line>`. Use a reviewed diff link where a deletion is clearer in context. Mention an open review comment as already raised if it covers the confirmed finding.
- When authorized to post, confirm the PR still points to the reviewed head, then submit the report as one review comment with pinned source links using `gh pr review <number-or-url> --comment --body-file <report>`.
