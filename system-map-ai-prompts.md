# System Map AI Prompts

## 1. Describe the System
Create a system map for this project. List all of the services used by this project, the ports, frameworks and connection types between them. Display an ASCII diagram of the system.

Print the diagram to the terminal.

Grafana is dependant on Prometheus. All of the services should have their dependencies clearly indicated with arrow connections as well. Create a new diagram with this in mind and output it to the terminal.

Create a single diagram of the system. Find every service and connection between them. For each connection display the direction and type (HTTP, database query, pub/sub event, etc.). Port numbers and frameworks should be labeled as well. Each service dependency should have an arrow with its proper direction and connection type. Do not add anything beyond the services and their connections. Output as an ASCII diagram in the terminal.

Be sure to include the connection to and from the database on the diagram as well.

Take this latest diagram and write it to the system-map-ai.md file in the learn-ops-infrastructure directory.

Add the latest diagram to your memory.

## 2. Convert to a Mermaid Diagram

Check your memory for the diagram. Convert it to a Mermaid flowchart. Use Graph LR. Each of the services should have a labeled node. Each connection should have a directed edge showing the direction and connection type (HTTP, database query, pub/sub event, etc.) include the Port if applicable. Do not add anything beyond the services and their connections. Output only the Mermaid code block.
