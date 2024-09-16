# Conectándose al ledger

Después de haber instalado el CLI, podemos conectarnos a un ledger escribiendo:

```powershell
minka server connect https://[ledger].[Server]
```

```bash
$ minka server connect https://ach.ldg-dev.one/api/v2
? Server URL: <ledger URL>**

✅ Connected to server <server name> (<server URL>)

Active ledger: <ledger name>
```

La URL del ledger requerida como entrada tiene el formato `https://<ledger>.<server>`. Puedes obtener la URL de tu ledger de la URL de tu estudio eliminando la parte `/studio/...`. Por ejemplo, si la URL es [`https://ach.example.com/studio`](https://ach.ldg-dev.one/studio), la URL del ledger que necesitas usar es `https://ach.example.com/api/v2`.

Podemos probar que todo está funcionando correctamente iniciando sesión como un banco de prueba. Nuestro ledger RTP viene con un banco de prueba preconfigurado, el nombre de este banco es Tesla Bank (`teslabank`). Para iniciar sesión como este banco, usa el siguiente comando:

```
$ minka  login
? Signer: teslabank
? Signer password for teslabank *[hidden]*

✅ Logged in as teslabank.
```

> La <mark style="color:red;">contraseña</mark> para **teslabank** es **`tesla`**.
