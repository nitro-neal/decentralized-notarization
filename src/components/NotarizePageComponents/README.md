# Creating a notarized document utilizing verifiable credentials 

  **This process creates a digital notarization of a document utilizing signing with private keys of verifiable credentials.**

At a high end the overall process utilizes ethereum public / private keys to create a signed JWT object that is of the registered Texas digital notarization schema. We then take this JWT and put it on a ledger for immutable storage.

## Steps


 1. First the notary fill out all valid information about the document including: the Image to notarize, her notary id, her notary seal, the custodians name among other information about the document.
 2. If the notary does not have an ethereum private key they can just enter their email and the email to did service will generate a private key for them and send them an email with their private key.
 3. When they add a custodians email in the background the email to did service will get the address of the custodian
 4. With the notaries public / private key, the custodians public key, and all the document information we can now generate a digital notarization. 
 5. All on the frontend the a pdf is created that includes all the information necessary about the digital notarization.
 6. In the VC JWT a hash of the document is created, along with an encrypted hash of the notarizes official Texas notary pem key.
 7. Once this VC JWT is created we store it on the ledge of choice.
 8. If it is stored on Amazon S3, the frontend requests a signed url from Amazon api gateway to store the document on s3 directly.
 9. If it is stored on Eth it can either A. call Eth To Did service backend endpoint to store onto the ethereum ledger or B. if a notary provides a funding private key with funds it can execute the transaction from the frontend.


## Uploading JWT to AWS S3

 
 We used signed urls to upload from the frontend to S3 allowing for serverless architecture for JWTs uploaded be stored on S3.

![enter image description here](https://d2908q01vomqb2.cloudfront.net/1b6453892473a467d07372d45eb05abc2031647a/2020/09/09/s3-2.png)
More information can be found here - https://aws.amazon.com/blogs/compute/uploading-to-amazon-s3-directly-from-a-web-or-mobile-application/

## Adding JWT to ethereum ledger

More information on how the JWT is stored to the blockchain can be found here - https://github.com/cityofaustin/mypass/tree/main/services/blockchain

An overview of the process is here:
[![](https://mermaid.ink/img/eyJjb2RlIjoic2VxdWVuY2VEaWFncmFtXG5GdW5kaW5nIFdhbGxldCAtPj4gRG9jdW1lbnQgRElEIFdhbGxldDogRXN0aW1hdGUgY29zdCwgc2VuZCBmdW5kc1xuRG9jdW1lbnQgRElEIFdhbGxldC0-PkRpZCBSZWdpc3RyeSBDb250cmFjdDogSldUIEdldHMgc3RvcmVkIGluIEJsb2NrY2hhaW5cbk5vdGUgcmlnaHQgb2YgRG9jdW1lbnQgRElEIFdhbGxldDogV2FpdCBmb3IgY29uZmlybWF0aW9uXG5Eb2N1bWVudCBESUQgV2FsbGV0LT4-IEZ1bmRpbmcgV2FsbGV0OiBTZW5kIHJlbWFpbm5nIGZ1bmRzIGJhY2sgKGlmIGFueSkiLCJtZXJtYWlkIjp7InRoZW1lIjoiZGVmYXVsdCIsInRoZW1lVmFyaWFibGVzIjp7ImJhY2tncm91bmQiOiJ3aGl0ZSIsInByaW1hcnlDb2xvciI6IiNFQ0VDRkYiLCJzZWNvbmRhcnlDb2xvciI6IiNmZmZmZGUiLCJ0ZXJ0aWFyeUNvbG9yIjoiaHNsKDgwLCAxMDAlLCA5Ni4yNzQ1MDk4MDM5JSkiLCJwcmltYXJ5Qm9yZGVyQ29sb3IiOiJoc2woMjQwLCA2MCUsIDg2LjI3NDUwOTgwMzklKSIsInNlY29uZGFyeUJvcmRlckNvbG9yIjoiaHNsKDYwLCA2MCUsIDgzLjUyOTQxMTc2NDclKSIsInRlcnRpYXJ5Qm9yZGVyQ29sb3IiOiJoc2woODAsIDYwJSwgODYuMjc0NTA5ODAzOSUpIiwicHJpbWFyeVRleHRDb2xvciI6IiMxMzEzMDAiLCJzZWNvbmRhcnlUZXh0Q29sb3IiOiIjMDAwMDIxIiwidGVydGlhcnlUZXh0Q29sb3IiOiJyZ2IoOS41MDAwMDAwMDAxLCA5LjUwMDAwMDAwMDEsIDkuNTAwMDAwMDAwMSkiLCJsaW5lQ29sb3IiOiIjMzMzMzMzIiwidGV4dENvbG9yIjoiIzMzMyIsIm1haW5Ca2ciOiIjRUNFQ0ZGIiwic2Vjb25kQmtnIjoiI2ZmZmZkZSIsImJvcmRlcjEiOiIjOTM3MERCIiwiYm9yZGVyMiI6IiNhYWFhMzMiLCJhcnJvd2hlYWRDb2xvciI6IiMzMzMzMzMiLCJmb250RmFtaWx5IjoiXCJ0cmVidWNoZXQgbXNcIiwgdmVyZGFuYSwgYXJpYWwiLCJmb250U2l6ZSI6IjE2cHgiLCJsYWJlbEJhY2tncm91bmQiOiIjZThlOGU4Iiwibm9kZUJrZyI6IiNFQ0VDRkYiLCJub2RlQm9yZGVyIjoiIzkzNzBEQiIsImNsdXN0ZXJCa2ciOiIjZmZmZmRlIiwiY2x1c3RlckJvcmRlciI6IiNhYWFhMzMiLCJkZWZhdWx0TGlua0NvbG9yIjoiIzMzMzMzMyIsInRpdGxlQ29sb3IiOiIjMzMzIiwiZWRnZUxhYmVsQmFja2dyb3VuZCI6IiNlOGU4ZTgiLCJhY3RvckJvcmRlciI6ImhzbCgyNTkuNjI2MTY4MjI0MywgNTkuNzc2NTM2MzEyOCUsIDg3LjkwMTk2MDc4NDMlKSIsImFjdG9yQmtnIjoiI0VDRUNGRiIsImFjdG9yVGV4dENvbG9yIjoiYmxhY2siLCJhY3RvckxpbmVDb2xvciI6ImdyZXkiLCJzaWduYWxDb2xvciI6IiMzMzMiLCJzaWduYWxUZXh0Q29sb3IiOiIjMzMzIiwibGFiZWxCb3hCa2dDb2xvciI6IiNFQ0VDRkYiLCJsYWJlbEJveEJvcmRlckNvbG9yIjoiaHNsKDI1OS42MjYxNjgyMjQzLCA1OS43NzY1MzYzMTI4JSwgODcuOTAxOTYwNzg0MyUpIiwibGFiZWxUZXh0Q29sb3IiOiJibGFjayIsImxvb3BUZXh0Q29sb3IiOiJibGFjayIsIm5vdGVCb3JkZXJDb2xvciI6IiNhYWFhMzMiLCJub3RlQmtnQ29sb3IiOiIjZmZmNWFkIiwibm90ZVRleHRDb2xvciI6ImJsYWNrIiwiYWN0aXZhdGlvbkJvcmRlckNvbG9yIjoiIzY2NiIsImFjdGl2YXRpb25Ca2dDb2xvciI6IiNmNGY0ZjQiLCJzZXF1ZW5jZU51bWJlckNvbG9yIjoid2hpdGUiLCJzZWN0aW9uQmtnQ29sb3IiOiJyZ2JhKDEwMiwgMTAyLCAyNTUsIDAuNDkpIiwiYWx0U2VjdGlvbkJrZ0NvbG9yIjoid2hpdGUiLCJzZWN0aW9uQmtnQ29sb3IyIjoiI2ZmZjQwMCIsInRhc2tCb3JkZXJDb2xvciI6IiM1MzRmYmMiLCJ0YXNrQmtnQ29sb3IiOiIjOGE5MGRkIiwidGFza1RleHRMaWdodENvbG9yIjoid2hpdGUiLCJ0YXNrVGV4dENvbG9yIjoid2hpdGUiLCJ0YXNrVGV4dERhcmtDb2xvciI6ImJsYWNrIiwidGFza1RleHRPdXRzaWRlQ29sb3IiOiJibGFjayIsInRhc2tUZXh0Q2xpY2thYmxlQ29sb3IiOiIjMDAzMTYzIiwiYWN0aXZlVGFza0JvcmRlckNvbG9yIjoiIzUzNGZiYyIsImFjdGl2ZVRhc2tCa2dDb2xvciI6IiNiZmM3ZmYiLCJncmlkQ29sb3IiOiJsaWdodGdyZXkiLCJkb25lVGFza0JrZ0NvbG9yIjoibGlnaHRncmV5IiwiZG9uZVRhc2tCb3JkZXJDb2xvciI6ImdyZXkiLCJjcml0Qm9yZGVyQ29sb3IiOiIjZmY4ODg4IiwiY3JpdEJrZ0NvbG9yIjoicmVkIiwidG9kYXlMaW5lQ29sb3IiOiJyZWQiLCJsYWJlbENvbG9yIjoiYmxhY2siLCJlcnJvckJrZ0NvbG9yIjoiIzU1MjIyMiIsImVycm9yVGV4dENvbG9yIjoiIzU1MjIyMiIsImNsYXNzVGV4dCI6IiMxMzEzMDAiLCJmaWxsVHlwZTAiOiIjRUNFQ0ZGIiwiZmlsbFR5cGUxIjoiI2ZmZmZkZSIsImZpbGxUeXBlMiI6ImhzbCgzMDQsIDEwMCUsIDk2LjI3NDUwOTgwMzklKSIsImZpbGxUeXBlMyI6ImhzbCgxMjQsIDEwMCUsIDkzLjUyOTQxMTc2NDclKSIsImZpbGxUeXBlNCI6ImhzbCgxNzYsIDEwMCUsIDk2LjI3NDUwOTgwMzklKSIsImZpbGxUeXBlNSI6ImhzbCgtNCwgMTAwJSwgOTMuNTI5NDExNzY0NyUpIiwiZmlsbFR5cGU2IjoiaHNsKDgsIDEwMCUsIDk2LjI3NDUwOTgwMzklKSIsImZpbGxUeXBlNyI6ImhzbCgxODgsIDEwMCUsIDkzLjUyOTQxMTc2NDclKSJ9fX0)](https://mermaid-js.github.io/mermaid-live-editor/#/edit/eyJjb2RlIjoic2VxdWVuY2VEaWFncmFtXG5GdW5kaW5nIFdhbGxldCAtPj4gRG9jdW1lbnQgRElEIFdhbGxldDogRXN0aW1hdGUgY29zdCwgc2VuZCBmdW5kc1xuRG9jdW1lbnQgRElEIFdhbGxldC0-PkRpZCBSZWdpc3RyeSBDb250cmFjdDogSldUIEdldHMgc3RvcmVkIGluIEJsb2NrY2hhaW5cbk5vdGUgcmlnaHQgb2YgRG9jdW1lbnQgRElEIFdhbGxldDogV2FpdCBmb3IgY29uZmlybWF0aW9uXG5Eb2N1bWVudCBESUQgV2FsbGV0LT4-IEZ1bmRpbmcgV2FsbGV0OiBTZW5kIHJlbWFpbm5nIGZ1bmRzIGJhY2sgKGlmIGFueSkiLCJtZXJtYWlkIjp7InRoZW1lIjoiZGVmYXVsdCIsInRoZW1lVmFyaWFibGVzIjp7ImJhY2tncm91bmQiOiJ3aGl0ZSIsInByaW1hcnlDb2xvciI6IiNFQ0VDRkYiLCJzZWNvbmRhcnlDb2xvciI6IiNmZmZmZGUiLCJ0ZXJ0aWFyeUNvbG9yIjoiaHNsKDgwLCAxMDAlLCA5Ni4yNzQ1MDk4MDM5JSkiLCJwcmltYXJ5Qm9yZGVyQ29sb3IiOiJoc2woMjQwLCA2MCUsIDg2LjI3NDUwOTgwMzklKSIsInNlY29uZGFyeUJvcmRlckNvbG9yIjoiaHNsKDYwLCA2MCUsIDgzLjUyOTQxMTc2NDclKSIsInRlcnRpYXJ5Qm9yZGVyQ29sb3IiOiJoc2woODAsIDYwJSwgODYuMjc0NTA5ODAzOSUpIiwicHJpbWFyeVRleHRDb2xvciI6IiMxMzEzMDAiLCJzZWNvbmRhcnlUZXh0Q29sb3IiOiIjMDAwMDIxIiwidGVydGlhcnlUZXh0Q29sb3IiOiJyZ2IoOS41MDAwMDAwMDAxLCA5LjUwMDAwMDAwMDEsIDkuNTAwMDAwMDAwMSkiLCJsaW5lQ29sb3IiOiIjMzMzMzMzIiwidGV4dENvbG9yIjoiIzMzMyIsIm1haW5Ca2ciOiIjRUNFQ0ZGIiwic2Vjb25kQmtnIjoiI2ZmZmZkZSIsImJvcmRlcjEiOiIjOTM3MERCIiwiYm9yZGVyMiI6IiNhYWFhMzMiLCJhcnJvd2hlYWRDb2xvciI6IiMzMzMzMzMiLCJmb250RmFtaWx5IjoiXCJ0cmVidWNoZXQgbXNcIiwgdmVyZGFuYSwgYXJpYWwiLCJmb250U2l6ZSI6IjE2cHgiLCJsYWJlbEJhY2tncm91bmQiOiIjZThlOGU4Iiwibm9kZUJrZyI6IiNFQ0VDRkYiLCJub2RlQm9yZGVyIjoiIzkzNzBEQiIsImNsdXN0ZXJCa2ciOiIjZmZmZmRlIiwiY2x1c3RlckJvcmRlciI6IiNhYWFhMzMiLCJkZWZhdWx0TGlua0NvbG9yIjoiIzMzMzMzMyIsInRpdGxlQ29sb3IiOiIjMzMzIiwiZWRnZUxhYmVsQmFja2dyb3VuZCI6IiNlOGU4ZTgiLCJhY3RvckJvcmRlciI6ImhzbCgyNTkuNjI2MTY4MjI0MywgNTkuNzc2NTM2MzEyOCUsIDg3LjkwMTk2MDc4NDMlKSIsImFjdG9yQmtnIjoiI0VDRUNGRiIsImFjdG9yVGV4dENvbG9yIjoiYmxhY2siLCJhY3RvckxpbmVDb2xvciI6ImdyZXkiLCJzaWduYWxDb2xvciI6IiMzMzMiLCJzaWduYWxUZXh0Q29sb3IiOiIjMzMzIiwibGFiZWxCb3hCa2dDb2xvciI6IiNFQ0VDRkYiLCJsYWJlbEJveEJvcmRlckNvbG9yIjoiaHNsKDI1OS42MjYxNjgyMjQzLCA1OS43NzY1MzYzMTI4JSwgODcuOTAxOTYwNzg0MyUpIiwibGFiZWxUZXh0Q29sb3IiOiJibGFjayIsImxvb3BUZXh0Q29sb3IiOiJibGFjayIsIm5vdGVCb3JkZXJDb2xvciI6IiNhYWFhMzMiLCJub3RlQmtnQ29sb3IiOiIjZmZmNWFkIiwibm90ZVRleHRDb2xvciI6ImJsYWNrIiwiYWN0aXZhdGlvbkJvcmRlckNvbG9yIjoiIzY2NiIsImFjdGl2YXRpb25Ca2dDb2xvciI6IiNmNGY0ZjQiLCJzZXF1ZW5jZU51bWJlckNvbG9yIjoid2hpdGUiLCJzZWN0aW9uQmtnQ29sb3IiOiJyZ2JhKDEwMiwgMTAyLCAyNTUsIDAuNDkpIiwiYWx0U2VjdGlvbkJrZ0NvbG9yIjoid2hpdGUiLCJzZWN0aW9uQmtnQ29sb3IyIjoiI2ZmZjQwMCIsInRhc2tCb3JkZXJDb2xvciI6IiM1MzRmYmMiLCJ0YXNrQmtnQ29sb3IiOiIjOGE5MGRkIiwidGFza1RleHRMaWdodENvbG9yIjoid2hpdGUiLCJ0YXNrVGV4dENvbG9yIjoid2hpdGUiLCJ0YXNrVGV4dERhcmtDb2xvciI6ImJsYWNrIiwidGFza1RleHRPdXRzaWRlQ29sb3IiOiJibGFjayIsInRhc2tUZXh0Q2xpY2thYmxlQ29sb3IiOiIjMDAzMTYzIiwiYWN0aXZlVGFza0JvcmRlckNvbG9yIjoiIzUzNGZiYyIsImFjdGl2ZVRhc2tCa2dDb2xvciI6IiNiZmM3ZmYiLCJncmlkQ29sb3IiOiJsaWdodGdyZXkiLCJkb25lVGFza0JrZ0NvbG9yIjoibGlnaHRncmV5IiwiZG9uZVRhc2tCb3JkZXJDb2xvciI6ImdyZXkiLCJjcml0Qm9yZGVyQ29sb3IiOiIjZmY4ODg4IiwiY3JpdEJrZ0NvbG9yIjoicmVkIiwidG9kYXlMaW5lQ29sb3IiOiJyZWQiLCJsYWJlbENvbG9yIjoiYmxhY2siLCJlcnJvckJrZ0NvbG9yIjoiIzU1MjIyMiIsImVycm9yVGV4dENvbG9yIjoiIzU1MjIyMiIsImNsYXNzVGV4dCI6IiMxMzEzMDAiLCJmaWxsVHlwZTAiOiIjRUNFQ0ZGIiwiZmlsbFR5cGUxIjoiI2ZmZmZkZSIsImZpbGxUeXBlMiI6ImhzbCgzMDQsIDEwMCUsIDk2LjI3NDUwOTgwMzklKSIsImZpbGxUeXBlMyI6ImhzbCgxMjQsIDEwMCUsIDkzLjUyOTQxMTc2NDclKSIsImZpbGxUeXBlNCI6ImhzbCgxNzYsIDEwMCUsIDk2LjI3NDUwOTgwMzklKSIsImZpbGxUeXBlNSI6ImhzbCgtNCwgMTAwJSwgOTMuNTI5NDExNzY0NyUpIiwiZmlsbFR5cGU2IjoiaHNsKDgsIDEwMCUsIDk2LjI3NDUwOTgwMzklKSIsImZpbGxUeXBlNyI6ImhzbCgxODgsIDEwMCUsIDkzLjUyOTQxMTc2NDclKSJ9fX0)

## Example of JWT
eyJ0eXAiOiJKV1QiLCJhbGciOiJFUzI1NkstUiJ9.eyJpYXQiOjE2MDM4MTgzODcsInN1YiI6ImRpZDpldGhyOjB4NkQzQkY0NDc5NjA2MUM3MzNBYUJmNjA3NTlmZkM1NTA1YzhDOUQ0YiIsIm5iZiI6MTYwMzgxODM4NiwidmMiOnsiQGNvbnRleHQiOlsiaHR0cHM6Ly93d3cudzMub3JnLzIwMTgvY3JlZGVudGlhbHMvdjEiLCJodHRwczovL3d3dy53My5vcmcvMjAxOC9jcmVkZW50aWFscy9leGFtcGxlcy92MSJdLCJpZCI6ImRpZDpldGhyOjB4NzBmQTZGMGEzZDk5MjQxODQzRDExOEE4NGQ4MjNlZDQ1NGI1MUNCQiIsInR5cGUiOlsiVmVyaWZpYWJsZUNyZWRlbnRpYWwiLCJUZXhhc05vdGFyeUNyZWRlbnRpYWwiXSwiaXNzdWVyIjp7ImlkIjoiZGlkOmV0aHI6MHg4NTQwY2I2OTAwZDlFOTQxZTMyOGFDNDljZjEzZTIwNzA4ODY2OWVEIiwiZW5zRG9tYWluIjoiZW1haWx0b2RpZC5ldGgiLCJub3RhcnlJZCI6MTIzNDUsIm5vdGFyeVB1YmxpY0tleSI6Ii0tLS0tQkVHSU4gUFVCTElDIEtFWS0tLS0tXG5NSUdmTUEwR0NTcUdTSWIzRFFFQkFRVUFBNEdOQURDQmlRS0JnUUNjWUdRZzY0U0lXVHpvQmlyZHkyZ0FOZjRWXG5jSmJENkxWK1hMSUF6eGNZbnlxVWUrYld0bUh0VmY4bUhLeS9HRjR1UnVFYUJyMDlFdDU1eUlPV2dXelVGMXhvXG5oV0V2cGwya0FJQmdxV29MeEdtUUNvdTJSeGRUbUFKU3Z3R2ZWbVliSGZMcXZYb2RaWTkvNHMzbkRqemxkN1hrXG4wSDdCUlRwNnlhb2ZoWW8zendJREFRQUJcbi0tLS0tRU5EIFBVQkxJQyBLRVktLS0tLSJ9LCJpc3N1YW5jZURhdGUiOjE2MDM4MTgzODY5NTIsImV4cGlyYXRpb25EYXRlIjoiMjAzMC0wMS0wMVQwMDowMDowMC4wMDBaIiwidmVyaWZpYWJsZVByZXNlbnRhdGlvblJlZmVyZW5jZSI6eyJpZCI6IiJ9LCJjcmVkZW50aWFsU3ViamVjdCI6eyJpZCI6ImRpZDpldGhyOjB4NkQzQkY0NDc5NjA2MUM3MzNBYUJmNjA3NTlmZkM1NTA1YzhDOUQ0YiIsImVuc0RvbWFpbiI6ImVtYWlsdG9kaWQuZXRoIiwiVGV4YXNEaWdpdGFsTm90YXJ5Ijp7InR5cGUiOiJjZXJ0aWZpZWRDb3B5Iiwic2lnbmVkRG9jdW1lbnRIYXNoIjoibThZR3RtQ25sRWpjQkU3T1YwdDhTdVRDV2pJNnVqRGNHdHdmeHBCekN5RUxxMGdGMm1TWS9Cek94UXl6dWJlVlE3WkRwdXdXRVBsOHYrZUxoRjdxQlBxYk01SnpyOHljOEFaOWs5TklFODdmZXREek5kVlJmakFlZkpnT3k1RDFVeVZFM0hPTmhxY1Z0OHI4U1gzUzdJZDFVM2RKS21hRU9TWW1BOEFWVVFNPSIsImhhc2hUeXBlIjoiTUQ1In19fSwiaXNzIjoiZGlkOmV0aHI6MHg4NTQwY2I2OTAwZDlFOTQxZTMyOGFDNDljZjEzZTIwNzA4ODY2OWVEIn0.RvarD1k5BWfck3Txp4LZ7-ez-lID6qgGf-cv51t9mfelOAy0cGMUhmwUy7dpcq0ERORzpQeoX5t-I3hXMF26EAE

Payload - 

   
  
 
 

    {
       "iat":1603818387,
       "sub":"did:ethr:0x6D3BF44796061C733AaBf60759ffC5505c8C9D4b",
       "nbf":1603818386,
       "vc":{
          "@context":[
             "https://www.w3.org/2018/credentials/v1",
             "https://www.w3.org/2018/credentials/examples/v1"
          ],
          "id":"did:ethr:0x70fA6F0a3d99241843D118A84d823ed454b51CBB",
          "type":[
             "VerifiableCredential",
             "TexasNotaryCredential"
          ],
          "issuer":{
             "id":"did:ethr:0x8540cb6900d9E941e328aC49cf13e207088669eD",
             "ensDomain":"emailtodid.eth",
             "notaryId":12345,
             "notaryPublicKey":"-----BEGIN PUBLIC KEY-----\nMIGfMA0GCSqGSIb3DQEBAQUAA4GNADCBiQKBgQCcYGQg64SIWTzoBirdy2gANf4V\ncJbD6LV+XLIAzxcYnyqUe+bWtmHtVf8mHKy/GF4uRuEaBr09Et55yIOWgWzUF1xo\nhWEvpl2kAIBgqWoLxGmQCou2RxdTmAJSvwGfVmYbHfLqvXodZY9/4s3nDjzld7Xk\n0H7BRTp6yaofhYo3zwIDAQAB\n-----END PUBLIC KEY-----"
          },
          "issuanceDate":1603818386952,
          "expirationDate":"2030-01-01T00:00:00.000Z",
          "verifiablePresentationReference":{
             "id":""
          },
          "credentialSubject":{
             "id":"did:ethr:0x6D3BF44796061C733AaBf60759ffC5505c8C9D4b",
             "ensDomain":"emailtodid.eth",
             "TexasDigitalNotary":{
                "type":"certifiedCopy",
                "signedDocumentHash":"m8YGtmCnlEjcBE7OV0t8SuTCWjI6ujDcGtwfxpBzCyELq0gF2mSY/BzOxQyzubeVQ7ZDpuwWEPl8v+eLhF7qBPqbM5Jzr8yc8AZ9k9NIE87fetDzNdVRfjAefJgOy5D1UyVE3HONhqcVt8r8SX3S7Id1U3dJKmaEOSYmA8AVUQM=",
                "hashType":"MD5"
             }
          }
       },
       "iss":"did:ethr:0x8540cb6900d9E941e328aC49cf13e207088669eD"
    }
