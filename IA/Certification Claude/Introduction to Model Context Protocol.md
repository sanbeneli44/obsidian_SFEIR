
MCP est une couche de communication conçu pour fournir Claude avec son contexte et ses outils

Les serveurs MCP donnent accès à un ensemble d'outils (github par exemple) qui exposent des fonctionnalités liées à un service externe.

L'avantage c'est que nous n'avons pas à écrire les différents schémas d'outils.

Les fournisseurs de service créent leurs propres implémentations (par exemple AWS)

MCP est séparé en deux parties
* **MCP Client** sur notre serveur
* **MCP Server** sur notre serveur ou externe au serveur local
Le MCP client va faire le lien entre notre serveur et le MCP server par différents protocoles (http, websocket...)

La communication entre le MCP Client et le MCP server est faire par message. Les messages autorisés sont tous définis dans les spécifications MCP par exemple listTools, callToolRequest![[Screenshot 2026-07-31 at 16.59.49.png]]

Sur un vrai projet, on implémente soit un MCP client soit un MCP server

Nous allons utiliser le SDK officiel MCP pour créer un MCP server
> from mcp.server.fastmcp import FastMCP
> mcp = FastMCP("DocumentMCP", log_level="ERROR")

Ce sdk permet également facilement la définition d'outils

La déclaration de l'outils nécessite un nom et une description ainsi qu'une fonctionne décrivant ce que fait l'outils![[Screenshot 2026-07-31 at 18.19.12.png]]

Anthropic pourra générer un schéma JSON de l'outils. Ce schéma pourra être passé à Claude

Le nom des tools ne doit pas contenir d'espace, à remplacer par un underscore



# The server inspector

L'inspecteur du serveur MCP permet de débugger

**Lancement MCP server inspector:** 
* uv venv
* source .venv/bin/activate  # On Windows: .venv\Scripts\activate
* uv run main.py
* mcp dev mcp_server.py

Les nouveaux outils sont listés dans l'onglet tools et en cliquant dessus, on peut manuellement invoqué l'outils

# Implementing client

MCP client est composé d'une classe. Cette classe va faire le lien avec une session cliente.

La session cliente va faire le lien avec le serveur MCP. Elle fait partie du SDK Python

Dans cette classe, il y a différentes méthodes comme
* **list_tools**: permettant de demande quel est la liste des tools accessibles par Claude
* **call_tools**: permettant à Claude d'utiliser un tools

## list_tools

Pour récupérer la liste des outils acessibles par claude, on va ajouter ce code dans le corps de la méthode:

> async def list_tools(self) -> list[types.Tool]:  
    result = await self.session().list_tools()  
    return result.tools


**self.session()**: permet d'accèder à la session et de se connecter au serveur MCP

## call_tools

Pour utiliser un outils par Claude, on va ajouter ce code dans le corps de la méthode:

> async def call_tool(  
    self, tool_name: str, tool_input: dict  
) -> types.CallToolResult | None:  
	  return await self.session().call_tool(tool_name, tool_input)

Les arguments de la méthode call_tools sont le nom de l'outils et la liste des arguments d'entrée de l'outils

## Test de ces deux fonctions

Pour tester les fonctions, on peut ajouter dans notre fichier mcp_client.py, une méthode main qui va afficher la liste des outils avec la fonction list_tools

> # For testing  
async def main():  
    async with MCPClient(  
        # If using Python without UV, update command to 'python' and remove "run" from args.  
        command="uv",  
        args=["run", "mcp_server.py"],  
    ) as _client:  
        result = await _client.list_tools()  
        print(result)

Pour tester et récupérer la liste des outils disponibles, il faudra exécuter la commande suivante:
> uv run mcp_client.py

# Définition Ressource MCP

Les ressources permettant au MCP server d'exposer certaines informations au MCP client. Par exemple, retourner la liste des documents disponibles

Il y a deux types de ressources:
* **direct**: a une uri static (par exemple docs://documents)
* **basée sur un modèle** a un ou plusieurs paramètres dans son uri (par exemple: docs://documents/{doc_id})
  Le nom du paramètre dans l'uri sera le même que le nom de la variable de la fonction python

Ces définitions de ressource sont à faire coté MCP Server
> @mcp.resource(  
    "docs://documents",  
    mime_type="application/json"  
)  
def list_docs() -> list[str]:  
    return list(docs.keys())  
  
> @mcp.resource(  
    "docs://documents/{doc_id}",  
    mime_type="text/plain"  
)  
def fetch_doc(doc_id: str) -> str:  
    if doc_id not in docs:  
        raise ValueError(f"Doc with id {doc_id} not found")  
    return docs[doc_id]

# Accès à une ressource

L'accès a une ressource coté MCP Client.

Une seule fonction est utilisée par les deux ressources car l'uri est passée en paramètre de la fonction

> async def read_resource(self, uri: str) -> Any:  
    result = await self.session().read_resource(AnyUrl(uri))  
    resource = result.contents[0]  
  
> 	   if isinstance(resource, types.TextResourceContents):  
	        if resource.mimeType == "application/json":  
	            return json.loads(resource.text)  
	        return resource.text


Claude n'a pas besoin d'utiliser le tools prédéfini précédemment pour lire le contenu du fichier mais il utilise la ressource

# Définition prompt

Si on veut ajouter une nouvelle feature, appelable, on utilisera un prompt que nous utiliserons comme des commandes customs permettant de générer des actions supportées par notre application

Une autocomplétion des prompts disponibles sera affichée par le MCP Client. Dans notre exemple, on va utiliser la commande/prompt **/format** pour reformater le contenu d'un fichier de plaintext en markdown

Claude peut formater le contenu des fichiers markdown sans utiliser de prompt. Les prompts sont spécialement conçus pour des traitements spécifiques définis par les prompts. Ces prompts pourront être réutilisés.

Un prompt a 
* un nom
* une description (optionel)


> @mcp.prompt(  
    name="format",  
    description="Rewrites the contents of the document in Markdown format."  
)  
def format_document(  
        doc_id: str = Field(description="Id of the document to format")  
) -> list[base.Message]:  
    prompt = f"""  
    Your goal is to reformat a document to be written with markdown syntax.  
    The id of the document you need to reformat is:    <document_id>    {doc_id}  
    </document_id>  
    Add in headers, bullet points, tables, etc as necessary. Feel free to add in structure.    Use the 'edit_document' tool to edit the document. After the document has been reformatted...    """  
    return [  
        base.UserMessage(prompt)  
    ]
    
Les prompts peuvent utiliser les tools déjà définis

# prompts dans MCP Client

Pour avoir l'autocomplete automamtique des commandes, on va ajouter cette fonction

> async def list_prompts(self) -> list[types.Prompt]:  
    result = await self.session().list_prompts()  
    return result.prompts

Pour utiliser les prompts définis dans le MCP server, on va ajouter cette fonction

> async def get_prompt(self, prompt_name, args: dict[str, str]):  
    result = await self.session().get_prompt(prompt_name, args)  
    return result.messages

# Résumé

**Tools**: Claude décide quand utiliser ces outils et les résultats sont utilisés par Claude
**Resources**: L'application décide quand appeler ces ressources, les ressources sont utilisées par l'application
**Prompts**: l'utilisateur décide quand appeler les ressources
