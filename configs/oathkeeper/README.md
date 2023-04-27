// Create new token 
1. Login as root to docker image bash: docker exec -it --user root <containerID> /bin/sh
2. Crate new token: oathkeeper credentials generate --alg RS256 > id_token.jwks.json
3. Copy file: docker cp <containerID>:/id_token.jwks.json .
(Extra info: Copy file to docker container like that: docker cp file.ext <container>:/path/to/file.ext)