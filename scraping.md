# Workflow
n8n = automation platform -> web or docker
postgresql = database -> local or docker

### 1 page
**table schema**
- **titles**: id, name
- **contents**: id, titles_id, page, data

**n8n flow:**
1. POST to website -> return HTML
2. distribute HTML boxes to a table
3. parse into text & divide (titles, sources, content) into columns

**postgres flow (loop for every title):**
1. insert title & get title_id
2. insert content (title_id, page, data)

### All Genesis
**table schema**
- **verses**: book_number, chapter, verse
- **titles**: id, verses_id, name
- **contents**: id, titles_id, page, data

**flow**
1. generate book, chapter, verse
2. LOOP for every combination: A (book, chapter, verse)
3. INSERT A into table VERSES
4. POST HTTP request using A
5. extract main HTML content
6. parse HTML into tables
7. LOOP for every titles: B
8. INSERT B into table TITLES
9. divide B into every page with its contents: C
10. INSERT C into table CONTENTS
