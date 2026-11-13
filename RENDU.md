# Rendu — Atelier Kubernetes

> Complétez ce fichier au fil de l'atelier, puis faites un commit et un push (voir la section « Rendu » du README).

**Nom / Prénom :** Tourville Alexis 
**Groupe :** B2

---

## Exercice 1 — Cluster

1. Combien de nœuds affiche `kubectl get nodes` ? Quel est le rôle de chacun ?
   > il affiche 3 noeuds, les noeuds agent on un role NONE et serveur est control plane
2. Que montre `docker ps` ? Qu'est-ce qu'un « nœud » dans k3d, concrètement ?
   > le nom des noeud, leurs image et leur status. c'est la machine, conteneur docker ici, qui fournit les ressources

## Exercice 2 — Premier déploiement

3. Quels objets Kubernetes ont été créés par `kubectl create deployment` ? (indice : `kubectl get all`)
   > Un Deployment a été créé. Il a ensuite créé un ReplicaSet, qui a créé le Pod web-... .
4. Vous avez supprimé le pod `web-...` : que s'est-il passé, et pourquoi ?
   > Le pod a été recréé automatiquement par le ReplicaSet pour maintenir le nombre de réplicas souhaité par le Deployment.

## Exercice 3 — Modifier un conteneur en direct

5. Après suppression du pod, votre message « Bienvenue... » a-t-il survécu ? Pourquoi ?
   > Non, Le message avait été modifié directement dans le conteneur et n'était pas enregistré dans l'image ni dans un volume persistant, le nouveau pod est reparti de l'image d'origine.
6. Pourquoi le `port-forward` a-t-il cessé de fonctionner après la suppression du pod ?
   > Le port-forward était relié à l'ancien pod. Quand celui-ci a été supprimé, la connexion s'est interrompue ; il faut relancer la commande vers le nouveau pod.

## Exercice 4 — Déclaratif

7. Quelle différence entre `kubectl create` et `kubectl apply` ?
   >kubectl create cree une nouvelle resource si elle existe pas et renvoie une erreur si elle existe, kubectl apply cree si elle existe pas et met a jours si elle existe a partir d'un fichier .yaml
8. À quoi sert le `selector` d'un Service ? Que se passe-t-il s'il ne correspond à aucun pod ?
   >il permet d'envoyer des infos a des pods et si il ne les trouvent pas il ne peut pas transmettre.

## Exercice 5 — Scaling & mises à jour

9. Sur quels nœuds se trouvent vos 3 réplicas ? Collez la sortie de `kubectl get pods -l app=site -o wide`.
   > Les trois réplicas sont répartis sur k3d-lab-agent-0, k3d-lab-server-0 et k3d-lab-agent-1.
   ```
   NAME                   READY   STATUS    RESTARTS   AGE     IP          NODE               NOMINATED NODE   READINESS GATES
   site-75775484b-77lsw   1/1     Running   0          3m57s   10.42.0.5   k3d-lab-agent-0    <none>           <none>
   site-75775484b-gfpgv   1/1     Running   0          3m57s   10.42.2.7   k3d-lab-server-0   <none>           <none>
   site-75775484b-nfl5c   1/1     Running   0          3m57s   10.42.1.7   k3d-lab-agent-1    <none>           <none>
   ```
10. Avec l'image `nginx:9.99`, quel statut avait le pod ? Quelle commande vous a donné la cause ?
    > Le nouveau pod était en ImagePullBackOff. kubectl describe pod <nom-du-pod> a montré que l'image nginx:9.99 est introuvabl.
11. Pendant la panne, le site restait-il accessible ? Pourquoi ?
    > Oui. Les trois anciens pods fonctionnels sont restés prêts et le Service a continué à leur envoyer le trafic, seul le nouveau pod ne pouvait pas démarrer.

## Exercice 6 — Portainer & Mario

12. URL publique de votre Mario :
    > https://glorious-adventure-5gxjg4vr54vrhp5q-8585.app.github.dev/
13. Retrouvez le Deployment `mario` en ligne de commande. Portainer et `kubectl` parlent-ils au même composant ? Lequel ?
    > Oui, les deux communiquent avec l'API Server de Kubernetes. Le Deployment est visible avec kubectl get deploy mario.

## Bonus (facultatif)

14. Bonus réalisés et remarques :
    > A : l'Ingress site répond sur le port 8081.
    B : readinessProbe HTTP et ressources ajoutées.
    C : après le drain d'agent-0, les pods ont été replanifiés sur agent-1 et server-0, ils ne reviennent pas automatiquement sur agent-0 après son rétablissement.
    D : le site est déployé dans prod avec 3 réplicas prêts.

---

**Score obtenu avec `./scripts/check.sh` :** ... / ...
