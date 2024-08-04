<table>
<tr>
<td>
This script is a basic example of the Lua programming language. It was created for personal use, but feel free to use or study it
</td>
</tr>
</table>

---

## Features
- Setup: Defines a maximum distance and sets up services and URLs for fetching server information.
- ListServers Function: Retrieves a list of public game servers.
- HopServer Function: Chooses a random server from the list and teleports the local player to that server.
- Main Loop: Periodically checks if any other players are within a specified distance from the local player. If so, it initiates a server hop; otherwise, it continues checking every 60 seconds.
