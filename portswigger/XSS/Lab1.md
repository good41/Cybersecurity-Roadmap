# Lab: Reflected XSS into HTML context with nothing encoded

## Vulnerability Summary

- Це вразливість з класу reflected XSS, яка дасть зловмиснику змогу виконувати довільний JS код з браузера жертви. 

- Проте слід зазначити, що цей підклас вразливістей (reflected) необхідно поєднувати
з соціальною інженерією, що робить його трохи складнішим для виконання.

## Attack Steps

1. При відкритті завдання у нас є пошукова строка куди ми введемо свій JS код для перевірки чи існує вразливість XSS.

<img width="1920" height="1080" alt="XSS 1_1" src="https://github.com/user-attachments/assets/b5cd9f3b-319d-43c8-bc73-4eb090016c3e" />
