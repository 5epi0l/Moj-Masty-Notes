

```javascript
async function getSite(url){
	const result = await fetch(url)
	const response = result.text()
	return response;
	
}


async function postSite(url, data)
{
	const response = await fetch(url, {
	'method':'POST',
	'mode':'})
}
```