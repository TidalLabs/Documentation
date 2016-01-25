How To Document Themes  

- There should be a README.md that gives a nice overview of how the theme is laid out, what each file does, etc
    - Markdown formatting
  - Talk briefly about special integration's (the concept of having the show metadata in that JSON file, that kinda thing)
  - Anything weird or funky should be briefly mentioned
  - Quick overview of the topology
  - In each of the theme files, simple PHP comments talking about what each thing does
  - If there's a custom `$_channel->posts` assignment or query, what does it do and why
  - The goal is to have a developer that's never seen our system before be able to quickly get a handle on what's going on
