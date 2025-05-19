In shell scripting, grouping commands lets you treat a sequence of operations as a single unit.  When you enclose commands in parentheses, you instruct the shell to launch a new subshell process, execute everything inside it, and then discard any side-effects—changes to the working directory, exported variables or file descriptor manipulations—once the group finishes.  Consider:

```bash
current="$PWD"
(
  cd /tmp
  touch example.txt
  echo "Inside subshell: $PWD"
)
echo "Back in original shell: $PWD"
```

Here, the `cd /tmp` and `touch example.txt` run in a separate shell.  The first `echo` displays `/tmp`, but after the parentheses complete, the outer shell remains in its original directory, as confirmed by the second `echo`.  Any variables exported or modified inside that subshell are invisible outside, because the subshell inherits a copy of the parent’s environment and then discards it on exit.

Subshells also provide a simple way to background an entire block of commands:

```bash
(
  date
  tar czf backup-$(date +%F).tar.gz /var/data
) &
```

In this example, both `date` and `tar` run together in the background, and their output or errors do not block the interactive session.  The main shell immediately regains control, while the grouped commands execute asynchronously.

By contrast, grouping with braces uses the current shell process, so any directory changes or variable assignments persist beyond the group:

```bash
{ cd /var/log; tar czf logs.tar.gz *.log; }
echo "Now still in: $PWD"   # will print /var/log
```

Note the required spaces after `{` and before `}`, and the terminating semicolon: braces form a command list without spawning a new process.

Because both forms count as a single command, you can apply redirections or conditional operators to the whole group.  To capture all output in one file, you might write:

```bash
{
  echo "Begin report"
  grep ERROR app.log
  echo "End report"
} > error_summary.txt
```

Whether you choose parentheses or braces depends on whether you want isolation of side-effects.  Parentheses offer a fresh environment—ideal for temporary working directories or background tasks—while braces run inline, allowing you to accumulate variables or remain in a different directory.  Mastering these two grouping mechanisms gives precise control over scope, I/O redirection and process management in your scripts.
