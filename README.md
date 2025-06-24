## 🔧 Configuration du projet Airflow avec SSH et MinIO

### 🖥️ Architecture

- `server-airflow` : héberge Apache Airflow
- `server-file` : contient les fichiers à récupérer via SSH
- `server-minio` : stockage objet MinIO

---

### 1️⃣ Générer une clé SSH sur `server-airflow`

Sur `server-airflow` :

```bash
ssh-keygen -t rsa -b 4096 -f ~/.ssh/id_rsa -N ""
```

> Cela crée :

- Clé privée : `/home/ubuntu/.ssh/id_rsa`
- Clé publique : `/home/ubuntu/.ssh/id_rsa.pub`

---

#### Copier la clé publique vers `server-file`

---

### 2️⃣ Créer une connexion SSH dans Airflow

Dans l'interface Airflow (`Admin > Connections`) :

- **Connection Id** : `ssh_connection`
- **Connection Type** : `SSH`
- **Host** : `<IP-server-file>`
- **Username** : `ubuntu`
- **Password** : *(laisser vide)*
- **Port** : `22`
- **Extra** :

```json
{
  "key_file": "/home/ubuntu/.ssh/id_rsa"
}
```

---

### 3️⃣ Créer une connexion MinIO dans Airflow

Dans `Admin > Connections` :

- **Connection Id** : `minio_connect`
- **Connection Type** : `Amazon Web Services`
- **AWS Access Key ID** : `minio-key`
- **AWS Secret Access Key** : `minio-secret`
- **Extra** :

```json
{
  "endpoint_url": "http://<IP-server-minio>:9000"
}
```

---

### 4️⃣  Créer le DAG `final.py`

Ajoutez votre DAG dans le dossier `dags/` de votre instance Airflow :

```bash
touch ~/airflow/dags/final.py
```

Rédigez votre logique de DAG à l’intérieur (connexion SSH + transfert + upload vers MinIO).
