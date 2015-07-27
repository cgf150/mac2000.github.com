---
layout: post
title: ElasticSearch search in attachments
tags: [elasticsearch, tika]
---

<iframe width="560" height="315" src="https://www.youtube.com/embed/ZDCBXX4S7uQ" frameborder="0" allowfullscreen></iframe>

Here is simplest possible demo of what elasticsearch can do:

**index.html**

    <!DOCTYPE html>
    <html>
    <head>
        <meta charset="UTF-8" />
        <meta name="viewport" content="width=device-width,initial-scale=1,user-scalable=no" />
        <title>Attachments Demo</title>
        <link rel="stylesheet" href="bower_components/bootstrap/dist/css/bootstrap.css" />
        <link rel="stylesheet" href="bower_components/fontawesome/css/font-awesome.css" />
        <style>
            html, body {
                height: 100vh;
            }

            mark {
                background: yellow;
            }

            .drop:after {
                content: 'Drop file here';
                pointer-events: none;
                position: fixed;
                left: 0;
                right: 0;
                top: 0;
                bottom: 0;
                background-color: #999;
                background-image: radial-gradient(rgba(0,0,0,0) 50%, rgba(0,0,0,1) 100%);
                opacity: .5;
                color: white;
                line-height: 100vh;
                text-align: center;
                font-size: 10vh;
                text-transform: uppercase;
                text-shadow: 1px 1px 5px #000;
            }
        </style>
    </head>
    <body>
        <div class="container" style="padding: 15px">
            <form class="row" data-bind="submit: reload">
                <p class="col-md-10">
                    <input type="search" class="form-control" data-bind="value: q" />
                </p>
                <p class="col-md-2">
                    <button class="btn btn-default btn-block" data-bind="click: reload">Filter</button>
                </p>
            </form>

            <ul class="list-group" data-bind="foreach: hits">
                <li class="list-group-item">
                    <div class="media">
                        <div class="media-body">
                            <i data-bind="css: icon"></i>
                            <strong data-bind="text: _source.name"></strong>
                        </div>
                        <div class="media-right">
                            <a class="text-danger" href="#" data-bind="click: $root.delete">delete</a>
                        </div>
                    </div>
                    <div data-bind="if: hasHighlights">
                        <ol data-bind="foreach: highlight.content">
                            <li data-bind="html: $data"></li>
                        </ol>
                    </div>
                </li>
            </ul>
        </div>
        <script src="bower_components/jquery/dist/jquery.js"></script>
        <script src="bower_components/bootstrap/dist/js/bootstrap.js"></script>
        <script src="bower_components/knockout/dist/knockout.debug.js"></script>
        <script src="bower_components/elasticsearch/elasticsearch.js"></script>
        <script>
            function Hit(data) {
                var self = this;

                ko.utils.extend(self, data);

                self.hasHighlights = self.highlight && self.highlight.content && self.highlight.content.length > 0;

                if(self._source.type === 'application/msword' || self._source.type === 'application/vnd.openxmlformats-officedocument.wordprocessingml.document') {
                    self.icon = 'fa fa-file-word-o';
                } else {
                    self.icon = 'fa fa-file-text-o';
                }
            }

            function Model() {
                var self = this;

                self.index = 'attachments';
                self.type = 'document'; // need to be replaced in mapping also

                self.client = new elasticsearch.Client({
                    host: 'localhost:9200',
                    log: 'trace'
                });

                self.q = ko.observable('');
                self.hits = ko.observableArray();

                self.reload = function() {
                    var body = {
                        index: self.index,
                        type: self.type,
                        body: {
                            _source: {
                                exclude: ['content']
                            }
                        }
                    };

                    if(self.q() && self.q().trim().length > 0) {
                        body.body.query = {
                                query_string: {
                                    query: self.q()
                                }
                        };

                        body.body.highlight = {
                            pre_tags: ['<mark>'],
                            post_tags: ['</mark>'],
                            fields: {
                                content: {}
                            }
                        };
                    }

                    self.client.search(body).then(function(body){
                        self.hits(body.hits.hits.map(function(hit){ return new Hit(hit); }));
                    }, alert);
                }

                self.delete = function(data) {
                    self.client.delete({
                        index: self.index,
                        type: self.type,
                        id: data._id,
                        ignore: [404]
                    }, function(err, body){
                        if(body.found) {
                            self.hits.remove(data);
                        }
                    });
                };

                function nop(event) {
                    event.stopPropagation();
                    event.preventDefault();
                    event.dataTransfer.dropEffect = 'copy';
                    document.body.className = event.type === 'dragleave' ? '' : 'drop';
                }
                document.body.addEventListener('dragenter', nop);
                document.body.addEventListener('dragover', nop, false);
                document.body.addEventListener('dragleave', nop);
                document.body.addEventListener('drop', function(event) {
                    event.stopPropagation();
                    event.preventDefault();

                    [].forEach.call(event.dataTransfer.files, function(file){
                        var reader = new FileReader();
                        reader.addEventListener('load', function(event){
                            self.client.create({
                                index: self.index,
                                type: self.type,
                                body: {
                                    name: file.name,
                                    date: (new Date(file.lastModified)).toISOString(),
                                    type: file.type,
                                    content: event.target.result.substr(event.target.result.indexOf(',') + 1)
                                }
                            }).then(function(body){
                                body._source = {
                                    name: file.name,
                                    date: (new Date(file.lastModified)).toISOString(),
                                    type: file.type
                                };
                                self.hits.push(new Hit(body));
                            });
                        });
                        reader.readAsDataURL(file);
                        document.body.className = '';
                    });

                }, false);


                self.mappings = {
                    index: self.index,
                    body: {
                        mappings: {}
                    }
                };

                self.mappings.body.mappings[self.type] = {
                    properties: {
                        name: {type: 'string'},
                        date: {type: 'date'},
                        type: {type: 'string'},
                        content: {
                            type: 'attachment',
                            fields: {
                                content:       {store: 'yes'},
                                author:        {store: 'yes'},
                                title:         {store: 'yes'},
                                date:          {store: 'yes'},
                                keywords:      {store: 'yes'},
                                _name:         {store: 'yes'},
                                _content_type: {store: 'yes'}
                            }
                        }
                    }
                };

                self.client.indices.exists({index: self.index}, function(body, exists){
                    if(!exists) {
                        self.client.indices.create(self.mappings);
                    } else {
                        self.reload();
                    }
                });
            }

            var model = new Model();
            ko.applyBindings(model);
        </script>
    </body>
    </html>

**bower.json**

    {
        "name": "attachments_demo",
        "version": "0.0.0",
        "dependencies": {
            "jquery": "~2.1.4",
            "bootstrap": "~3.3.5",
            "knockout": "~3.3.0",
            "fontawesome": "~4.3.0",
            "elasticsearch": "~5.0.0"
        }
    }

**elasticsearch.yml**

    cluster.name: attachments
    node.name: ${COMPUTERNAME}
    plugin.mandatory: mapper-attachments
    http.cors.enabled: true
    index.number_of_shards: 1
    index.number_of_replicas: 0

For this demo to work [mapper-attachments](https://github.com/elastic/elasticsearch-mapper-attachments) plugin should be installed.
