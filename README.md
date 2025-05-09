
## What Is a Jenkins Shared Library?

- A Shared Library in Jenkins is a way to manage reusable and repeatable code that can be used across multiple Jenkins pipelines. This is particularly useful when managing a large number of pipelines (e.g., 50 or more) that require similar logic or stages.

##  Why to Use a Shared Library ?

- Imagine you have a piece of common logic that is used in many pipelines.
- Instead of copying and pasting that code into each individual Jenkinsfile, you can define it once in a shared library.
- When a change is needed, you only update the shared library. All pipelines referencing it will automatically use the updated  code.
- This promotes consistency and simplifies maintenance.


## Advantages of Shared Libraries

    **Centralized Maintenance:**
    - Update logic in one place and reflect changes across all pipelines.

    **Standardization:**
    - Enforces a common structure or template, making pipelines more consistent and easier to understand.

    **Low-Code Pipelines:**
    - Reduces redundancy and minimizes code within individual Jenkinsfiles.

## Naming Conventions and Setup for Shared Library in Jenkins .

1. Naming Conventions for Groovy Files .
    - When naming Groovy files in a shared library, it’s recommended to follow camel casing. This means the file name should start with a lowercase letter, followed by uppercase letters for each new word. The file extension should be .groovy.

2. Configuring the Shared Library in Jenkins UI.
    - Navigate to Jenkins Configuration:  

        - Go to Manage Jenkins > Configure System.
        - Search for Global Pipeline Libraries.
        - Add a New Shared Library:
            - Under Global Pipeline Libraries, click Add.
            - Provide a name for the shared library.
            - In the Version field, specify the branch name you want to use (e.g., master or develop).
            - Under Git section, provide the Git repository URL where the shared library is hosted.

3. Folder Setup:
    - The shared library will automatically look for scripts in the vars folder, so you don't need to specify the folder name in the Jenkinsfile.

4. Save the Configuration:
    - Click Save to complete the configuration.

## How Shared Libraries Work (Conceptually) ?

- Pipeline uses: @Library('your-library-name')
- Jenkins fetches the shared library from a Git repo.
- Jenkins looks inside specific directories (vars/ or src/) for code it can execute.
- The code is sandboxed unless marked trusted (or executed by an admin).

## Importing the Shared Library in Jenkinsfile .

 1. Import the Library:

    - In your Jenkinsfile, import the shared library using the following syntax: @Library('name_of_shared_library') 
        - The 1 space underscore after @Library('') acts as a wildcard, meaning it will import all modules / functions available in the shared library, similar to the *(wildcard)  used in other programming languages.

 2. Calling Functions from the vars Folder:

    - Reference the Groovy files located inside the vars folder, not the functions inside those files.
    - For example, if you have a helloWorld.groovy file in the vars folder, you would call it directly like this : helloWorld()

 3. Execution:

    - Once the library is imported and the function is referenced, Jenkins will fetch the code from the GitHub repository and execute it as part of the pipeline.

## Appropriate Method to Configure a Shared Library .

- There are two parts to configuration .

    1. Shared Library Repository Structure : This is the Git repository where your shared library lives.

        my-first-shared-library/
            ├── vars/                           # Global functions or scripts
            │   └── testSharedLibrary.groovy    # Function callable from Jenkinsfile
            ├── src/                            # Optional Java/Groovy packages
            │   └── org/
            │       └── example/
            │           └── Helper.groovy
            └── README.md                       # Optional

            
        Example: vars/testSharedLibrary.groovy

        def call() {
                    echo 'Hello from the shared library!'
                }

        - Generally shared library is divided in 3 folders i.e. src/ ,vars/ ,resources/
            - vars/: Contains global variables and custom pipeline steps (defined as Groovy scripts).
            - src/: Houses helper classes for complex logic and utilities.
            - resources/: Used for templates or static files required by the pipeline.

    2. Jenkins Configuration (Global Pipeline Library)

     - Configure the shared library in Jenkins UI : i.e. Manage Jenkins → Configure System → Global Pipeline Libraries .

        | Field                 | Example                                                   |
        | --------------------- | --------------------------------------------------------- |
        | **Name**              | `my-first-shared-library`                                 |
        | **Default Version**   | `main`                                                    |
        | **SCM**               |  Git                                                      |
        | **Repository URL**    | `https://github.com/your-org/my-first-shared-library.git` |
        | **Credentials**       | Only if private repo                                      |
        | **Branches to build** | `*/main`                                                  |
        | **Implicit loading**  | (Optional) Check to auto-load in all pipelines            |


##  Final Directory Structures .
 
 - Shared Library Repo (Independent Git repo) .

            my-first-shared-library/
            ├── vars/
            │   └── testSharedLibrary.groovy
            └── src/
                └── org/
                    └── example/
                        └── Helper.groovy
    
 - Jenkins Project Repo .

            my-app-project/
                ├── Jenkinsfile         <-- Uses @Library
                └── application-code/


- NOTE : Here in this demonstration, we have used following repository i.e.

    - https://github.com/khannashiv/Jenkins-Practice (Jenkins project repo where Jenkins file resides at the root of Jenkins-Practice repo)
    - https://github.com/khannashiv/my-shared-library-for-jenkins.git (Independent Git repo i.e. my-shared-library-for-jenkins)