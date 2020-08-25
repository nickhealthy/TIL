# Learn about `git multiple repo`

* How to Interlocking multiple repositories within one repository

  1. Use Git Tools [(URL)][https://git-scm.com/book/en/v2/Git-Tools-Submodules] - `Submodules`

     * ```git bash
       git submodule add <url>
       
       ※ File is created: [.gitmodules, other_repo]
       ```

     * You can check the information by entering this command `git diff --cached --submodule`

     

  2. Use Git Tools [(URL)][https://git-scm.com/book/en/v2/Git-Tools-Submodules] - `submodules clone`

     * **Copying the repository including submodules**

     * ```git bash
       git submodule init
       ```

     * ```git bash
       git submodule update
       ```

       

       #### = same

     * ```git bash
       git clone --recurse-submodules
       ```

     

  3. **Update the repository including submodules**

     * ```git bash
       git fetch
       ```

     * ```git bash
       git merge origin/master
       ```

       

       #### = same

       ```
       git submodule update --remote
       ```

       