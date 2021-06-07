# hellvas
I need kanoe help with best security for each lisence in this project there all As is this is going to be lisence 9 of open source
Run actions/github-script@626af12fe9a53dc2972b48385e7fe7dec79145c9
  with:
    github-token: ***
    script: // Only perform this action with GitHub employees
  try {
    await github.teams.getMembershipForUserInOrg({
      org: 'github',
      team_slug: 'employees',
      username: context.payload.sender.login,
    });
  } catch(err) {
    // An error will be thrown if the user is not a GitHub employee
    // If a user is not a GitHub employee, we should stop here and
    // Not send a notification
    return
  }
  
  // Don't perform this action with Docs team members
  try {
    await github.teams.getMembershipForUserInOrg({
      org: 'github',
      team_slug: 'docs',
      username: context.payload.sender.login,
    });
    // If the user is a Docs team member, we should stop here and not send
    // a notification
    return
  } catch(err) {
    // An error will be thrown if the user is not a Docs team member
    // If a user is not a Docs team member we should continue and send
    // the notification
  }
  
  const issueNo = context.number || context.issue.number
  
  // Create an issue in our private repo
  await github.issues.create({
    owner: 'github',
    repo: 'docs-internal',
    title: `@${context.payload.sender.login} confirm that \#${issueNo} should be in the public github/docs repo`,
    body: `@${context.payload.sender.login} opened https://github.com/github/docs/issues/${issueNo} publicly in the github/docs repo, instead of the private github/docs-internal repo.\n\n@${context.payload.sender.login}, please confirm that this belongs in the public repo and that no sensitive information was disclosed by commenting below and closing the issue.\n\nIf this was not intentional and sensitive information was shared, please delete https://github.com/github/docs/issues/${issueNo} and notify us in the \#docs-open-source channel.\n\nThanks!`,
    labels: ['OS confirmation'],
  });
  
  core.setOutput('did_warn', 'true')
  
    debug: false
    user-agent: actions/github-script
    result-encoding: json
