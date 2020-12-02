# webmon

Webmon is three things combined:
1. An experimental [napari](https://napari.org/) shared memory client.
2. A proof of concept [Flask-SocketIO](https://flask-socketio.readthedocs.io/en/latest/) webserver.
3. An example web app that contains two pages:
    * `/viewer` a [three.js](https://threejs.org/) (WebGL) display of what tiles are visible in napari.
    * `/loader` with two very simple [Vega-Lite](https://vega.github.xo/vega-lite/) graphs related to the `ChunkLoader`.

See the lengthy napari [PR 1909](https://github.com/napari/napari/pull/1909) for more information.

# Screenshots

![tiles](https://user-images.githubusercontent.com/4163446/100827155-188b8680-342a-11eb-92bb-217321705947.png)

![graphs](https://user-images.githubusercontent.com/4163446/100827017-b763b300-3429-11eb-94c0-77c5110dc275.png)

# Summary

There is a shared memory connection between napari (Python) and this little webmon process (Python). And there is an always-on Websocket connection between webmon (Python) and the web app (Javascript). Messages can flow through both hops at 30-60Hz. Obviously there is some limit to the message size before things bog down. Limit is TBD.

Beyond messages, the Big Kahuna would be using shared memory buffers to back `numpy` arrays and `recarrays`. Then we could share large chunks of binary data. Throughput limits are not known yet. Particularly the Websocket hop might be the slow part. This has not been attempted yet.

If the websocket hop is the slow part, could an Electron process be a shared memory client, and then directly render the data? TBD.

# Python Requirements

* Python 3.9
    * Newest shared memory features were first added in Python 3.8.
    * However 3.8 seemed to have bugs, where 3.9 works.
* In webmon directory: `pip3 install -r requirements.txt`

# Javascript Requirements

* Install node/npm
    * Not sure of min req but on MacOS I've been using:
    * `node -v` -> `v14.3.0`
    * `npm -v` -> `6.14.4`
* In webmon directory: `make build`

# To modify Javascript files

* Do not edit .js files under `static`.
    * .json files in static are fair game.
* Edit .js files under `js` then build as above.
* If you made a Javascript-only change:
   * `make build`
   * Typically hard reload (shift-command-R) in Chrome is enough.
   * Typically do not need to restart napari/webmon unless you changed those.
   
# What Can I Do?

* Issues and PR's needed, including this README.
* Improve or modify the existing web app pages.
* Create new web app pages.
* Modify napari to send out more interesting data.
* Today probably need to modify `napari_client.py` and `webmon.py` in webmon.
    * Ideally longer term webmon will just "pass through" most data.
    * So to add something to just modify napari and the Javascript, nothing else.
    * We are not there yet, but it's clearly heading that way for many cases.
* Try sharing `numpy` data backed by a shared memory buffer.

Pretty soon we'll probably need a feature where web apps can "register" for messages they want to receive. So napari is not spewing all possible messages all the time. It will only send what's strictly needed right then.

# Python Shared Memory

* [multiprocessing.shared_memory](https://docs.python.org/3/library/multiprocessing.shared_memory.html) (official docs)
* [Python Shared Memory in Multiprocessing](https://mingze-gao.com/posts/python-shared-memory-in-multiprocessing/) (`numpy recarray`)
* [Interview With Davin Potts](https://www.vertica.com/blog/one-on-one-davin-potts-3-news-for-upcoming-python-release-3-8/) (core contributor)

![tweet](https://user-images.githubusercontent.com/4163446/100826307-090b3e00-3428-11eb-80ca-84c704b3ff5d.png)

![hn](https://user-images.githubusercontent.com/4163446/100826691-e7f71d00-3428-11eb-8438-ebca491d6f1a.png)

# Derived From
* [FlaskTest](https://github.com/ageller/FlaskTest)
    * A simple demo that combines Flask, Socket-IO, and three.js/WebGL
