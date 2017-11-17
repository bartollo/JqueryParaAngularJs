# JqueryParaAngularJs
Codificando uma página em Jquery para Angular Js

Este repositório foi criado para demonstrar uma forma simples de reescrever um web app que utiliza o framework Jquery para Angular Js.

Versão Jquery: http://www.bartollo.com.br/angular/index-jquery.html
Versão Angular Js: http://www.bartollo.com.br/angular/index.html

Foram realizados os seguintes passos:

1- Inclusão da biblioteca Angular JS:
<script src="https://ajax.googleapis.com/ajax/libs/angularjs/1.4.8/angular.min.js"></script>

2- Criação do Controller após a linha de inclusão do angular.min.js:
    <script type="text/javascript">
        angular.module('card', [])
            .controller('MainCtrl', function($scope, $http, $window) {});
    </script>


3 - Inclusão do atributo ng-app na tah html, referenciando o angular.module:
<html lang="en" ng-app="card">
  
3- Inclusão do atributo ng-controller na tag body, referenciando o único Controlador criado para gerenciar as ações da web app:
<body ng-controller="MainCtrl as ctrl">

4- Para configurar o loader da página foi necessário inserir o atributo ng-show na div, que subistituirá as funções Jquery .show() e .hide():
  <div class="se-pre-con" ng-show="loading"></div>
 
5- Foi criada uma variável dentro do controller para ser usada como flag para exeibir e inibir o Loader.
$scope.loading = true;

6 - Ao dar início a function, a variável recebrá valor true. No final de cada function, através do método .finally() , o flag receberá  false. Podendo assim ser controlada a exibição da div, sempre que iniciar e terminar uma function.

7 - Para realizar todas as tarefas do arquivo em Jquery, as chamadas  $.ajax , foram substituidas pelas chamadas $http.get $http.post e $http.delete:

7.1 - Faz o GET de todos os CARDS:
$http.get('http://bartollo.com.br/angular/cards/').
                then(function(response) {
                    $scope.cards = response.data;
                }).finally(function() {
                    // Hide loading spinner whether our call succeeded or failed.
                    $scope.loading = false;
                });


7.2 - Faz POST para as curtidas dos CARDS:
$scope.Curtir = function(item) {
                    $scope.loading = true;

                    $http.post('http://bartollo.com.br/angular/cards/like/', 'id=' + item.id, {
                        headers: {
                            'Content-Type': 'application/x-www-form-urlencoded; charset=UTF-8'
                        }
                    }).
                    then(function(response) {
                        item.likes = response.data.qtd_likes;
                        $window.alert('like com sucesso!');

                    }).finally(function() {
                        // Hide loading spinner whether our call succeeded or failed.
                        $scope.loading = false;
                    });

                };
                
7.3 - Faz DELETE para exclusão do card:
$scope.Deletar = function(item) {
                    $scope.loading = true;

                    var data = {
                      id:item.id
                    };

                    $http.delete('http://bartollo.com.br/angular/cards/?id='+item.id).
                    then(function(response) {
                        console.log(response);

                        if (response.data.success) {
                            $window.alert('excluído com sucesso!');
                            // console.log(item);
                            var index = $scope.cards.data.indexOf(item);
                            //console.log(index);
                            $scope.cards.data.splice(index, 1);

                        }
                    }).finally(function() {
                        // Hide loading spinner whether our call succeeded or failed.
                        $scope.loading = false;
                    });

                };
                
7.4 - Faz POST para incluir CARDS:
$scope.CriarCard = function(card) {
                    $scope.loading = true;

                    $http.post('http://bartollo.com.br/angular/cards/', 'url_imagem=' + card.url_imagem + '&descricao=' + card.descricao, {
                        headers: {
                            'Content-Type': 'application/x-www-form-urlencoded; charset=UTF-8'
                        }
                    }).
                    then(function(response) {
                        console.log(response);

                        if (response.data.success) {

                            $window.alert('inserido com sucesso!');
                            angular.copy({}, card);
                            $scope.$apply();

                            $http.get('http://bartollo.com.br/angular/cards/').
                            then(function(response) {
                                $scope.cards = response.data;
                            });

                        }
                    }).finally(function() {
                        // Hide loading spinner whether our call succeeded or failed.
                        $scope.loading = false;
                    });

                };

7.5 - Faz GET para atualizar os dados:
$scope.Atualizar = function() {
                    $scope.loading = true;

                    $http.get('http://bartollo.com.br/angular/cards/').
                    then(function(response) {
                        $scope.cards = response.data;
                    }).finally(function() {
                        // Hide loading spinner whether our call succeeded or failed.
                        $scope.loading = false;
                    });
                }
                
8 - O formulário de inclusão sofreu as alterações:

8.1 - Os inputs receberam os atributos ng-model , de acorco com os itens 7.4:

<input id="url_imagem" name="url_imagem" type="text" placeholder="" class="form-control input-md" required="" ng-model="card.url_imagem">

<input id="descricao" name="descricao" type="text" placeholder="" class="form-control input-md" required="" ng-model="card.descricao">

8.2 - Os botões receberam os atributos ng-click, assim como , ou link atualizar do header, já que a chamada da inclusão é feita com html5:

8.2.1 - Formulário:
<button ng-click="LimparForm(card)" class="btn btn-secondary" id="limpar-card">Limpar</button>
<button ng-click="CriarCard(card)" class="btn btn-primary" id="salvar-card">Salvar</button>
           
8.2.2 - Header:

<li class="nav-item active">
            <a class="nav-link" href="#" ng-click="Atualizar()">Atualizar
<span class="sr-only">(current)</span>

8.3 - Para limpar o formulário foi criado uma function específica:
 $scope.LimparForm = function(formModel) {
                    angular.copy({}, formModel);
                }

9 - A listagem dos CARDS foi feita numa div, dentro da div com class="ROW", utilizando o atributo ng-repeat :

<div class="row">

    <div class="col-lg-4 col-sm-6 portfolio-item" ng-repeat="item in cards.data">
        <div class="card h-100">
            <a href="#"><img class="card-img-top" src="{{item.url_imagem}}" alt=""></a>
            <div class="card-body">
                <h4 class="card-title">
   <button class="btn btn-success curtir" data-id="{{item.id}}" ng-click="Curtir(item)"> Curtir (<span class="quantidade-likes">{{item.likes}}</span>)</button>
   <button class="btn btn-danger deletar" data-id="{{item.id}}"  ng-click="Deletar(item)">Deletar</button>
 </h4>
                <p class="card-text">{{item.descricao}}</p>
            </div>
        </div>
    </div>

</div>
            
            


