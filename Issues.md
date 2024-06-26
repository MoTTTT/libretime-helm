# Issues and changes

- [ ] liquidsoap not connecting to api or icecast.

## liquidsoap not connecting to api or icecast

- `requests.exceptions.RetryError: HTTPConnectionPool(host='web.radio.svc.cluster.local', port=8080): Max retries exceeded with url: /api/v2/info (Caused by ResponseError('too many 504 error responses'))`
- ` WARNING  | libretime_playout.liquidsoap.client._client:wait_for_version:62 - could not get version: [Errno 111] Connection refused`
- Liquidsoap-playout logs:

```text
playout 2024-06-26 18:33:35,271 | INFO     | libretime_playout.main:cli:100 - Timezone: ('UTC', 'UTC')
playout 2024-06-26 18:33:35,272 | INFO     | libretime_playout.main:cli:101 - UTC time: 2024-06-26 18:33:35.272146
playout 2024-06-26 18:33:36,326 | WARNING  | urllib3.connectionpool:urlopen:870 - Retrying (Retry(total=2, connect=None, read=None, redirect=None, status=None)) after connection broken by 'NewConnectionError('<urllib3.connection.HTTPConnection object at 0x7f7cd4e89bd0>: Failed to establish a new connection: [Errno 111] Connection refused')': /api/version?format=json
playout 2024-06-26 18:33:37,864 | ERROR    | libretime_api_client._client:_request:93 - 500 Server Error: Internal Server Error for url: http://web.radio.svc.cluster.local:8080/api/version?format=json
playout 2024-06-26 18:33:39,931 | ERROR    | libretime_api_client._client:_request:93 - 500 Server Error: Internal Server Error for url: http://web.radio.svc.cluster.local:8080/api/version?format=json
playout 2024-06-26 18:33:41,984 | ERROR    | libretime_api_client._client:_request:93 - 500 Server Error: Internal Server Error for url: http://web.radio.svc.cluster.local:8080/api/version?format=json
playout 2024-06-26 18:33:44,207 | ERROR    | libretime_api_client._client:_request:93 - 500 Server Error: Internal Server Error for url: http://web.radio.svc.cluster.local:8080/api/register-component?format=json&component=pypo
playout 2024-06-26 18:33:44,207 | ERROR    | libretime_playout.main:wait_for_legacy:55 - 500 Server Error: Internal Server Error for url: http://web.radio.svc.cluster.local:8080/api/register-component?format=json&component=pypo
playout Traceback (most recent call last):
playout   File "/src/libretime_playout/main.py", line 48, in wait_for_legacy
playout     legacy_client.register_component("pypo")
playout   File "/usr/local/lib/python3.10/site-packages/libretime_api_client/v1.py", line 178, in register_component
playout     return self._base_client.register_component(component=component)
playout   File "/usr/local/lib/python3.10/site-packages/libretime_api_client/v1.py", line 51, in register_component
playout     return self._request(
playout   File "/usr/local/lib/python3.10/site-packages/libretime_api_client/_client.py", line 94, in _request
playout     raise exception
playout   File "/usr/local/lib/python3.10/site-packages/libretime_api_client/_client.py", line 89, in _request
playout     response.raise_for_status()
playout   File "/usr/local/lib/python3.10/site-packages/requests/models.py", line 1024, in raise_for_status
playout     raise HTTPError(http_error_msg, response=self)
playout requests.exceptions.HTTPError: 500 Server Error: Internal Server Error for url: http://web.radio.svc.cluster.local:8080/api/register-component?format=json&component=pypo
playout 2024-06-26 18:33:54,788 | WARNING  | libretime_playout.liquidsoap.client._client:wait_for_version:62 - could not get version: [Errno 111] Connection refused
2024-06-26 18:54:45,151 | WARNING  | libretime_playout.liquidsoap.client._client:wait_for_version:62 - could not get version: [Errno 111] Connection refused                                                                │
Traceback (most recent call last):                                                                                                                                                                                          │
  File "/usr/local/bin/libretime-playout", line 8, in <module>                            
    sys.exit(cli())                                                                       
  File "/usr/local/lib/python3.10/site-packages/click/core.py", line 1157, in __call__ return self.main(*args, **kwargs)                                                     
  File "/usr/local/lib/python3.10/site-packages/click/core.py", line 1078, in main  rv = self.invoke(ctx)                                                                 
  File "/usr/local/lib/python3.10/site-packages/click/core.py", line 1434, in invoke return ctx.invoke(self.callback, **ctx.params)                                        
  File "/usr/local/lib/python3.10/site-packages/click/core.py", line 783, in invoke    return __callback(*args, **kwargs)                                                    
  File "/src/libretime_playout/main.py", line 118, in cli wait_for_liquidsoap(liq_client)                                                       
  File "/src/libretime_playout/main.py", line 61, in wait_for_liquidsoap liq_version = liq_client.wait_for_version()                                           
  File "/src/libretime_playout/liquidsoap/client/_client.py", line 66, in wait_for_version raise LiquidsoapClientError("could not get liquidsoap version")                       
libretime_playout.liquidsoap.client._client.LiquidsoapClientError: could not get liquidsoap version  
```

## Changes

- [ ] Add clusterIssuer to helm chart

### Local helm chart development

Testing turn-around for chart changes, for flux deployment.

- To force chart renewal, and verify the installed manifest set, provide minor version change for the chart {helm package, helm repo index, publish}
- Force pull of new chart by update chart release version, and commit.
- Monitor helm release on k8s cluster for helm chart issues, fix and repeat
- When helm release passes syntactic testing and deployment, monitor pods for issues, fix and repeat

For faster development to test turnaround, skip versioning, packaging, index, publishing, and gitops:

- Undeploy radio app in flux
- Deploy via dev workstation local helm api, with local paths intact:

```bash
helm install -f /Users/martincolley/workspace/podzonedev-gitops/radio.thruhere.net/radio-values.yaml --namespace radio myradio /Users/martincolley/workspace/libretime-helm/charts/libretime
```