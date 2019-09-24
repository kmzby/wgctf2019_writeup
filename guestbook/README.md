1. Проверили nmap'm на открытые порты - ничего не нашли 
2. Попробовали стянуть фаилы dirsearch'm - снова ничего 
3. Долго пробовали найти инъекции в форме поста, потом в /search 
4. search прогнали sqlmap'm ничего не нашли, попробовали инъекцию в шаблоны, пришла идея что это может быть lua 
5. нагуглили вот такой документ http://www.syhunt.com/en/index.php?n=Articles.LuaVulnerabilities
6. Перепробовали все от туда и нашли search="\"..(io.open('cat\x20/usr/local/openresty/nginx/conf/nginx.conf','r'):read('*a'))..\""
7. Основной флаг нашли в /flag
8. в run.sh нашли подсказку про openresty
9. в /usr/local/openresty/nginx/conf/nginx.conf нашли доп
