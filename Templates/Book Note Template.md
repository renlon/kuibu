<%*
let modDatetime =  tp.file.last_modified_date("dddd Do MMMM YYYY HH:mm:ss");
let bookName = await tp.system.prompt("Book name to be added:")
let docTitle = `${bookName}-Notes`;
await tp.file.rename(docTitle);
-%>
---
book_name: <% bookName %>
created: <% tp.file.creation_date() %>
modified_time: <% modDatetime %>
draft: false
title:  <% docTitle %>
tags:  
---
# Summary

# FAQ

# Chapters
