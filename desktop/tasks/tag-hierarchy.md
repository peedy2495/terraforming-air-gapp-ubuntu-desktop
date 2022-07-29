# Used tag-hierarchy in this "desktop" role:

#under construction!
  - ntp # all ntp relevant tasks
    - timesyncd # setup related to timesyncd
  - software # everything handles software
    - software-all # everything
      - driver-nvidia
      - software-default # default packages
        - repocleanup # remove everything in sources.list.d
    - apt
      - repositories
        - repocleanup