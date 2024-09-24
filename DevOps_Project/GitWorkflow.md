# Git Workflow

A **Git workflow** is a set of practices or a structured process for managing changes to a project's source code using Git. It defines how branches are used, how code is committed, reviewed, merged, and deployed, ensuring collaboration between multiple developers is efficient and conflict-free. Different workflows are designed based on the team size, project complexity, and deployment strategies.

### Key Concepts in Git Workflow:

1. **Branching Model:**
Git uses branches to isolate development work. Different workflows define how branches are created, merged, and used. Common branches include:
    - **`main` or `master`:** The default production branch that contains stable, deployable code.
    - **`develop`:** Used for ongoing development, containing the latest work from developers.
    - **Feature branches:** Isolated branches for working on new features.
    - **Release branches:** For finalizing code before releasing to production.
    - **Hotfix branches:** For quickly applying critical fixes in production.
2. **Commits:**
Each change to the project is tracked as a **commit** in Git. Commits should be atomic, meaning they focus on a single task or bug fix and have clear commit messages explaining the change.
3. **Merging:**
When changes on a branch are complete, they are merged back into the main or develop branch, bringing together work from different developers.
4. **Pull Requests (PRs):**
A **pull request** is a GitHub feature where developers can propose changes, and those changes are reviewed and discussed before being merged. This step ensures code quality and alignment with project goals.
5. **Tagging:**
Tags in Git mark specific points in history (often used for releases) to track versions easily.

### Benefits of Using a Git Workflow:

- **Collaboration:** Multiple developers can work on different parts of the project without affecting each other’s work.
- **Version Control:** Every change is tracked, and previous versions can be restored if needed.
- **Continuous Integration (CI/CD):** Changes can be automatically tested and deployed as soon as they are merged.
- **Clear Structure:** Using branches for features, releases, and hotfixes keeps the project organized.

### Initialize the Repository

Start a new repository, create an empty commit, and set up the main and develop branches.

```bash
git init
git commit --allow-empty -m "Initial commit"
git branch -M main
git checkout -b develop
```

### **Create a Feature Branch**

For each new feature or change, create a new feature branch from `develop`. Make changes, commit them, and merge the feature branch back into `develop` once the feature is complete.

```bash
git checkout -b feature/new-feature
echo "print('New Feature')" > feature.py
git add feature.py
git commit -m "Add new feature"
git checkout develop
git merge feature/new-feature
```

### **Create a Release Branch**

When preparing for a release, create a release branch from `develop`. Finalize the release, update version numbers or documentation if necessary, and merge the release branch into `main` and `develop`.

```bash
git checkout -b release/1.0.0
echo "print('Release 1.0.0')" > release.py
git add release.py
git commit -m "Prepare release 1.0.0"
git checkout main
git merge release/1.0.0
git tag -a 1.0.0 -m "Release 1.0.0"
git checkout develop
git merge release/1.0.0
```

### **Create a Hotfix Branch**

For critical fixes on production, create a hotfix branch from `main`. Apply the fix and merge it into both `main` and `develop`.

```bash
git checkout -b hotfix/1.0.1
echo "print('Hotfix 1.0.1')" > hotfix.py
git add hotfix.py
git commit -m "Apply hotfix 1.0.1"
git checkout main
git merge hotfix/1.0.1
git tag -a 1.0.1 -m "Hotfix 1.0.1"
git checkout developA
git merge hotfix/1.0.1
```

### **Add the Remote and Push the Branches**

Once everything is merged locally, push your branches and tags to GitHub.

```bash
git remote add origin https://github.com/nileshmuthal1317/project-repo.git
git push -u origin main
git push -u origin develop
git push -u origin feature/new-feature
git push -u origin release/1.0.0
git push -u origin hotfix/1.0.1
```

![image2%202.png](image2%202.png)

![image3%201.png](image3%201.png)
