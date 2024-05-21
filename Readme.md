# Solr tutorial
Preparing docker container:
<ul>
<li> <b>docker</b> volume create solr9
<li> docker build . -t mysolr
<li> docker run -d --env-file solr_env.dist -p 8983:8983 --mount source=solr9,target=/var/solr --name my_solr mysolr solr-precreate Clients
</ul>
