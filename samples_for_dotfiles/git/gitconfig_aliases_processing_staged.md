# LLMs :love: GIT

Clickbait title is always good.

## give LLM to take a look at `git diff --staged` and laugh at you or be brutally honest critique...

Here are some examples how to add to `~/.gitconfig` `[alias] section to process staged changes via LLM. Here for processing via LLM is used mods: <https://github.com/charmbracelet/mods/> . 

Note: it's educational example so to keep it simple we use mods with prompt and default system message. However you may prefer to play with `mods --settings` and system prompts, to use below e.g. `mods --role git-staged-critic`...

Note: It's more for entertaiment then real work tool, as LLM will be ONLY given what you see with `git diff --staged` . Nevertheless, it still may catch typo or brainstorming way inspire you with some suggestion, or just `git joke` entertaiin ;).

So after applying below changes, one can `git add` changes, and:

* `git joke` - to get nerdy joke about changes to be commited (fun exercise: make it, so when provided commit or commit range it will make joke about those ! That way you can joke about other's commits ;). Also add to promopt info about vibe of jokes you like to make about other's commits ;).
* `git critic|glow` - will be brutally honest critic of changes
* `git sanity-check|glow` - I run out of ideas how to make good prompt for this one.
* and you don't need to use `|glow` if you prefer raw and streaming output :). Otherwise if you always want to use it... just enhance git alias to always pipe through it! Your setup, your choice! You are got of your config files! You are in charge of your perception of reality... at least on Linux machine, to a degree ;).

Ofc. mods supports many models, so it's easy to change to model and provider you desire.


```
[alias]
    ci = commit
    staged = diff --staged
    joke-staged = "!f() { if [ -z \"$(git diff --cached)\" ]; then echo 'No changes to be committed. Please `git add` changes to make this work.'; else git diff --cached | mods --no-cache -m gpt-4o -r 'Make short nerdy joke about changes you see.'; fi; }; f"
    joke = joke-staged
    critic-staged = "!f() { if [ -z \"$(git diff --cached)\" ]; then echo 'No changes to be committed. Please `git add` changes to make this work.'; else git diff --cached | mods --no-cache -m gpt-4o -r 'You are `git diff --staged` changes reviewer. Focus on changes made by user only, and provide concise response. When making sanity check, instead of rewriting all changes that user made, focus on things that you are concerened about, and WHY. Start with your WHY. Be brutally honest critic of changes to be introduced. User is about to commit those staged changes and is asking you for brutally honest opinion. Assume high readability and technically sophisticated reader. Levarage jardon and terminology of the domain, to minimize amount of words and maximize amount of information conveyed in structured way. Use multiple multi level lists formatting paragraphs metaphores, code snippets, mix language with code, to be maximally information efficient and concise.'; fi; }; f"
    critic = critic-staged
    crit = critic
    sanity-check-staged = "!f() { if [ -z \"$(git diff --cached)\" ]; then echo 'No changes to be committed. Please `git add` changes to make this work.'; else git diff --cached | mods --no-cache -m gpt-4o -r 'You are `git diff --staged` changes reviewer. Focus on changes made by user only, and provide concise response. When making sanity check, instead of rewriting all changes that user made, focus on things that you are concerened about, and WHY. Start with your WHY. Therefore, Sanity check of changes to be introduced. User is about to commit those staged changes and is asking you for sanity check (pay attention at all possible security and reliability inplications of changes that are considered to be done). Assume high readability and technically sophisticated reader. Levarage jardon and terminology of the domain, to minimize amount of words and maximize amount of information conveyed in structured way. Use multiple multi level lists formatting paragraphs metaphores, code snippets, mix language with code, to be maximally information efficient and concise.'; fi; }; f"
    sanity-check = sanity-check-staged
    sc = sanity-check
```

## drafting commit message:

For making commit message for staged:

```
    cm = "!f() { git diff --cached | mods --no-cache -m gpt-4o -r 'Make commit message for provided diff. Write only commit message, starting with first line summary (following conventional commits standard), then followed with concise information dense summary, levaraging on the fact that reader is highly technical sophisticated expert. Make rest very concise, information dense, maximum information, and minimization of words, utilize bullet lists (and for bullets use asterix * symbol).'; }; f"
```

To combine `commit` (ci) with `cm`(commit message) , we define `cim` :

```
    cim = !sh -c \"git commit -v -e -F <(git cm)\"
```

You may always want to amend, so it's not necesarily "LLM" strictly but ofen used:

```
    ciam = commit --amend
```


## Utils

When working with git aliases etc, it may get confusing for agents and git if you are in the root of git repo,
so you may find this `git gitprefix` handy:

```
    gitprefix = "!f() { echo \"GIT_PREFIX=$GIT_PREFIX\"; cd \"$GIT_PREFIX\"; pwd; }; f"
```

or not necessarily agents, here is example handy "add single file and commit" example use of it:

```
    addci = "!f() { (cd \"$GIT_PREFIX\" && git add \"$@\" && git commit -m \"+= $*\"); }; f"
```

### MARKERS - empty commits where you use commit message for later cleanup with `git rebase --interactive`

As title says. Sometimes you want to play around , experiment with different ways of fixing something,
and do cleanup later.

My workflow for this is `git WIP` , and later once it works `git GREEN` ,
so I have fricionless way of making clear markers in `git rebase -i` view, to work with.

with `ciae` I can add custom message if I want, e.g. `git ciae 'MANIFESTING ABUNDANCE OF COMMITS'`.


```
    ciae = commit --allow-empty -m
    green = commit --allow-empty -m GREEN
    wip = commit --allow-empty -m WIP
    dirty = commit --allow-empty -m DIRTY
    tosqash = commit --allow-empty -m SQUASH
    tos = commit --allow-empty -m SQUASH
```

## Other useful tools to combine

* mods: Apart already mentioned: <https://github.com/charmbracelet/mods/>
    * hopefully soon also rendering streaming data: <https://github.com/charmbracelet/glow/issues/601>
* glow: It's also useful sometimes to pipe through `glow` <https://github.com/charmbracelet/glow> for markdown rendering.
