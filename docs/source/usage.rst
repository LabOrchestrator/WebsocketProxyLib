Usage
=====

.. _installation:

Installation
------------

To use WebsocketProxyLib, first install it using pip:

.. code-block:: console

   (.venv) $ pip3 install lab-orchestrator-ws-proxy-lib

Creating a Proxy
----------------

To create a Proxy you need to create a ``lab_orchestrator_ws_proxy_lib.ws_proxy_lib.WebsocketProxy`` object:

.. autoclass:: lab_orchestrator_ws_proxy_lib.ws_proxy_lib.WebsocketProxy
    :members: __init__, run, run_in_thread, stop_thread
    :undoc-members:
    :exclude-members: clientToServer, serverToClient, proxy

    .. rubric:: Methods


.. autofunction:: lab_orchestrator_ws_proxy_lib.ws_proxy_lib.WebsocketProxy.__init__
    :noindex:

* The `remote_url` parameter is the base URL to the Kubernetes api (for example `"ws://localhost:8001"`).
* The `api_path` parameter is the path in the api that points to a VMI. This needs to contain the variables `{namespace}` and `{vmi_name}` (for example `"/apis/subresources.kubevirt.io/v1alpha3/namespaces/{namespace}/virtualmachineinstances/{vmi_name}/vnc"`). The variables will be replaced when a new connection is made with the correct namespace name and the VMI-name.
* The `local_dev_mode` parameter is a boolean that indicated if you are running the lib locally in a development mode or running it in a Kubernetes cluster. Running it locally disables ssl. Running it in Kubernetes will automatically include the TLS client certificate from `/var/run/secrets/kubernetes.io/serviceaccount/ca.crt` and use the token from `/var/run/secrets/kubernetes.io/serviceaccount/token`.
* The `secret_key` parameter is the key that is used to decrypt the token.
* The `host_path_prefix` when the server is started behind a reverse proxy it is possible that the path contains a prefix. For example `/ws_proxy/token/vm`.

Run Websocket Proxy
-------------------

After creating a proxy you need to run it. For this case you have the ``lab_orchestrator_ws_proxy_lib.ws_proxy_lib.WebsocketProxy.run`` method:

.. autofunction:: lab_orchestrator_ws_proxy_lib.ws_proxy_lib.WebsocketProxy.run
    :noindex:

The run method starts the websocket server in foreground and every new connection is added to an event loop.

Run Websocket Proxy in Thread
---------------------------------

Another way to start the proxy is the ``lab_orchestrator_ws_proxy_lib.ws_proxy_lib.WebsocketProxy.run_in_thread`` method:

.. autofunction:: lab_orchestrator_ws_proxy_lib.ws_proxy_lib.WebsocketProxy.run_in_thread
    :noindex:

This method runs the websocket server in background.

Stop Websocket Proxy Thread
---------------------------------

To stop the thread you can use the ``lab_orchestrator_ws_proxy_lib.ws_proxy_lib.WebsocketProxy.stop_thread`` method:

.. autofunction:: lab_orchestrator_ws_proxy_lib.ws_proxy_lib.WebsocketProxy.stop_thread
    :noindex:

Connect
-------

The proxy works as follows:

It creates a websocket at the given host and port. Then when you want to access a VM you need to call the websocket with a path that contains the token and the VM-name divided by a slash. Example: ``localhost:5001/ABCTOKENDEF/ubuntu``. The example contains the token ``ABCTOKENDEF`` and tries to access the VM with the name ``ubuntu``. The token contains a list of allowed VM-names and if the given VM-name is part of the token you will be able to access the VM.

Example CURL call:

.. code-block:: console

   $ curl \
    --header "Connection: Upgrade" \
    --header "Upgrade: websocket" \
    --header "Sec-WebSocket-Version: 13" \
    --header "Sec-WebSocket-Key: wPu8b8WOqAJZCYo95uVRDA==" \
    --include --no-buffer \
    localhost:5001/ABCTOKENDEF/ubuntu


Example
-------

An example can be found `here <https://github.com/LabOrchestrator/WebsocketProxy/blob/main/src/lab_orchestrator_ws_proxy/__main__.py>`_.

