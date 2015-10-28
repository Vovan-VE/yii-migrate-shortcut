Yii2 migration tool shortcut
============================

Here are some tiny shell scripts to shortcut [Yii2](https://github.com/yiisoft/yii2)
migration tool usage.

What and why
------------

###The problem #1

You are using atomic migrations instead of fat migrations, that can
fail in a middle.

DML migrations always use transactions - no problems.

DDL migration should be atomic. If non-atomic migration will fail
in a middle, you will either hack the migration temporary or manually
undo applied part of the migration.

### The problem #2

So, you need a number of tiny DDL migrations. But you are lazy to do
like following:

    $ ./yii migrate/create do_foo
    Create new migration '.../.._do_foo.php'? (yes|no) [no]:<y><Enter>
    $ ./yii migrate/create do_bar
    Create new migration '.../.._do_bar.php'? (yes|no) [no]:<y><Enter>
    $ ./yii migrate/create do_baz
    ...

and even like following:

    $ ./yii migrate/create do_foo
    New migration created successfully.
    $ ./yii migrate/create do_bar
    New migration created successfully.
    $ ./yii migrate/create do_baz
    ...

Solution
--------

Create all migrations with one command:

    $ ./migrate-create-multi do_foo do_bar do_baz

If you are using Bash, you can use word expansion also:

    $ ./migrate-create-multi do_{foo,bar,baz}
    # do same things as above

Live example:

    $ ./migrate-create-multi create_{foo,bar,baz,lol} add_fk_{baz_{foo,bar},lol_bar}
    ...
    $ git status
    On branch ...
    Untracked files:
      (use "git add <file>" ...)

        migrations/m151028_085142_create_foo.php
        migrations/m151028_085143_create_bar.php
        migrations/m151028_085144_create_baz.php
        migrations/m151028_085145_create_lol.php
        migrations/m151028_085146_add_fk_baz_foo.php
        migrations/m151028_085147_add_fk_baz_bar.php
        migrations/m151028_085148_add_fk_lol_bar.php

More lazy
---------

I'm lazy to type `migrate/<subcommand>` without autocompletion
just after `./yii` . Here is one more shortcut script.

Just migrate whatever available:

    $ ./migrate

    Total 7 new migrations to be applied:

        m151028_085142_create_foo
        m151028_085143_create_bar
        m151028_085144_create_baz
        m151028_085145_create_lol
        m151028_085146_add_fk_baz_foo
        m151028_085147_add_fk_baz_bar
        m151028_085148_add_fk_lol_bar

    *** applying m151028_085142_create_foo
        > ...
    *** applying m151028_085142_create_foo (time: ...)
    ...

    Migrated up successfully.

Run subcommand:

    $ ./migrate redo 7

    Total 7 new migrations to be redone:
    ...

    Migration redone successfully.

Well done.

Usage in a project
------------------

Here is just an example.

1.  Add `*~` either to `.git/info/exclude` or `.gitignore` for backups to you project.
2.  Make a symlink or copy `tpl/migration.tpl.php` from here to `tmp~/migration.tpl.php`
    in your project.
3.  Make symlink `mn-migrate-create~` (semantic: "migration new") in your project
    to script `migrate-create-multi`.
4.  Make symlink `mg-migrate~` (semantic: "migrate" or "migration go") in your project
    to script `migrate`.

Ready. Now you can do something like this:

    $ ./mn<TAB>
    $ ./mn-migrate-create~ do_foo do_bar ...
    ...
    # edit migrations
    $ ./mg<TAB>
    $ ./mg-migrate~
    ...
    $ ./mg<TAB>
    $ ./mg-migrate~ redo 2
    ...
