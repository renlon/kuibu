<%*
let modDatetime =  tp.file.last_modified_date("dddd Do MMMM YYYY HH:mm:ss");
let docTitle = await tp.system.prompt("Blog title to be added:");
await tp.file.rename(docTitle);
-%>
---
created: <% tp.file.creation_date() %>
modified_time: <% modDatetime %>
draft: false
title:  <% docTitle %>
tags:  
---


# References
