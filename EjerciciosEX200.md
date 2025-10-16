
1. <details>
   <summary> Loguearte en un host como un usuario determinado</summary>  
   <br>
  
    ```console
      [student@workstation ~]$ ssh student@serverb
    ```
   </details>

2. <details>
   <summary> Crear un directorio</summary>
   <br>
  
    ```console
      [student@serverb ~]$ mkdir grading
    ```
   </details>

3. <details>
   <summary> Crear tres ficheros vacios grade1,grade2 y grade3 </summary>
    <br>
  
    ```console
      [student@serverb ~]$ touch grading/grade{1,2,3}
    ```
</details>

4. <details>
   <summary> Visualizar el contenido de /bin/manage y redirigirlo a un fichero /grading/review.txt </summary>
   <br>

   ```console
      [student@serverb ~]$ head -5 bin/manage > grading/review.txt
   ```
  </details>

5. <details>
   <summary> Visualizar las últimas tres lineas del fichero manage y añadelo a review.txt </summary>
   <br>

    ```console
      [student@serverb ~]$ tail -3 bin/manage >> grading/review.txt
    ```
   </details>

6. <details> 
   <summary> Copiar /home/student/grading/review.txt a /home/student/grading/review-copy.txt</summary>
   <br>
  
    ```console
      [student@serverb grading]$ cp review.txt review-copy.txt</details>
    ```
    </details>

7. <details>
   <summary> Crear un hard link de /home/student/hardcopy a /home/student/grading/grade1 </summary>
   <br>

   ```console
     [student@serverb ~]$ ln grading/grade1 hardcopy
     [student@serverb ~]$ ls -l grading/grade1
     -rw-r--r--. 2 student student 0 Mar 6 16:45 grading/grade1
   ```

8. <details>
   <summary> Crear un soft link o enlace simbolico de /home/student/softcopy /home/student/gading/grade2 </summary>
   <br>

   ```console
     [student@serverb ~]$ ln -s grading/grade2 softcopy
     [student@serverb ~]$ ls -l softcopy
     lrwxrwxrwx. 1 student student 14 Mar 6 17:58 softcopy -> grading/grade2
   ```
   
</details>
