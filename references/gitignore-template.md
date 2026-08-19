# .gitignore Template

Generate this file at the project root with entries matching the chosen tech stack. Remove sections that don't apply.

## Python
__pycache__/
*.py[cod]
*.egg-info/
dist/
*.egg
.venv/
venv/
.python-version

## Node / TypeScript
node_modules/
.next/
dist/
build/
*.tsbuildinfo

## Go
vendor/
*.exe
*.test
*.out

## Environment
.env
.env.local
.env.*.local
*.key
*.pem

## IDE
.idea/
.vscode/
*.swp
*.swo
*~
.DS_Store

## Database (local dev only)
*.db
*.sqlite3

## OS files
.DS_Store
Thumbs.db