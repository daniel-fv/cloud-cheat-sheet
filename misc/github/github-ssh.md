# Conectarse por SSH a Github desde Azure Shell
> Como mejor práctica, cada punto de origen y destino de conexión debe contar con una llave. Por ejemplo, Azure Shell debe contar con una llave para conectarse Github pero si queremos conectarnos desde PowerShell debemos crear otra llave para ese fin.

Usar SSH para conectarnos a Github para clonar repositorios es una forma más segura que clonarlos por HTTPS ya que usamos una llave de conexión en vez de usuario y contraseñas.

Para poder clonar repositorios de Github dentro del Azure Shell tenemos primero que generar una llave de seguridad.

## Nos conectamos al Azure Shell 
![](images/2021-07-04-18-14-03.png)

## Generamos la llave para ssh
Usamos el correo de nuestra cuenta de Github
```sh
ssh-keygen -t ed25519 -C "daniel@condor.com.ni"
```

Para guardarla con un nombre específico usamos
```sh
ssh-keygen -t ed25519 -C "daniel@condor.com.ni" -f ~/.ssh/github_key
```

Cuando corramos el comando nos pedirá crear un passphrase que es nuestra contraseña para abrir la llave cuando lo necesitemos.

```powershell
Generating public/private rsa key pair.
Enter file in which to save the key (/home/daniel/.ssh/github_key):
Enter passphrase (empty for no passphrase):
Enter same passphrase again:
Your identification has been saved in /home/daniel/.ssh/github_key.
Your public key has been saved in /home/daniel/.ssh/github_key.pub.
The key fingerprint is:
SHA256:******************************************* daniel@condor.com.ni
The key's randomart image is:
+---[RSA 3072]----+
|+.   +yX*o .     |
|... ..E+*=o      |
|  ..o.=E=.o      |
|   . * =.o .     |
|    . S o o..    |
|       + .oo     |
|        S+.  .   |
|        ..+.+    |
|          o*..   |
+----[SHA256]-----+
```


Una vez hecho esto, se nos generan dos archivos, los cuales se guardarán en el directorio de ssh de nuestro directorio personal:

/home/daniel/.ssh/

Los archivos que se generan son:
- github_key (llave privada)
- github_key.pub (llave pública)

## Configuramos ssh-agent para que use la llave correspondiente según el dominio
Creamos el archivo config
```bash
nano ~/.ssh/config 
```
Agregamos
```
Host github.com
  HostName github.com
  User git
  IdentityFile ~/.ssh/github_key
  IdentitiesOnly yes
```
Así, cada vez que nos conectemos a github por ssh, buscará la llave de github por predeterminada.
## Agregamos la llave pública a nuestra cuenta de Github

La llave pública **(github_key.pub)** es la que copiaremos a Github. La llave privada no la compartiremos con nadie más.

Copiamos la llave para después pegar en nuestra cuenta de Github. 

Para esto, primero la mostramos con:

```powershell
cat /home/daniel/.ssh/github_key.pub
```



En la esquina superior derecha hacemos click en la foto de nuestro perfil y después en **Settings**

![](https://docs.github.com/assets/images/help/settings/userbar-account-settings.png)
!

Entramos a opciones de **SSH and GPG keys**

![](https://docs.github.com/assets/images/help/settings/settings-sidebar-ssh-keys.png)

Agregamos una llave nueva
![](https://docs.github.com/assets/images/help/settings/ssh-add-ssh-key.png)

Pegamos el texto del archivo de la llave
![](https://docs.github.com/assets/images/help/settings/ssh-key-paste.png)

## Clonamos con ssh

Una vez que tengamos ya la llave pública en Github podremos clonar desde Azure Shell sin tener que ingresar contranseña de Github.

Copiamos

![](2021-07-04-19-51-12.png)

Clonamos
```bash
$ git clone git@github.com:nova-cloud/guia-github.git/
```
Nos pedirá la contraseña (passphrase) de la llave para conectarnos.

## Referencia
- https://docs.github.com/es/authentication/connecting-to-github-with-ssh/generating-a-new-ssh-key-and-adding-it-to-the-ssh-agent

- https://www.freecodecamp.org/news/how-to-manage-multiple-ssh-keys/

- https://hackernoon.com/how-to-authenticate-your-git-to-github-with-ssh-keys
