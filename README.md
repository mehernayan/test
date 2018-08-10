HttpInterceptor and custom NoCache Interceptor

app.config(['$httpProvider', function($httpProvider) {
    // This is used for telling AngularJS to enable post requests By CORS

    $httpProvider.defaults.useXDomain = true;
    delete $httpProvider.defaults.headers.common['X-Requested-With'];
    $httpProvider.defaults.withCredentials = true;
    $httpProvider.interceptors.push('noCacheInterceptor');
    //To Break IE Cache
  // $httpProvider.defaults.headers.common['Cache-Control'] = 'no-cache';
  // $httpProvider.defaults.headers.common['Pragma'] = 'no-cache';
  }]).factory('httpInterceptor', ['$q', '$rootScope','$location', '$window',
      function ($q, $rootScope, $location, $window) {
      return {
        'request': function(config) {
           // request your $rootscope messaging should be here?
          return config;
       // interceptor code
          localStorage.setItem("userIds", userId);
          var userIdVal = localStorage.getItem("userIds");
           if(userIdVal == "null") {
              $location.url('/login')
          }
          
        },

       'requestError': function(rejection) {
          // request error your $rootscope messagin should be here?
          return $q.reject(rejection);
        },


        'response': function(response) {
          // response your $rootscope messagin should be here?
        
          return response;
          console.log(response);
          
        },

       'responseError': function(response) {
          // response error your $rootscope messagin should be here?
          if (response.status === 401 || response.status === 403 ) {
           //$window.localStorage.clear();
          //$window.sessionStorage.clear();
          if(response.data && response.data.message){
            $rootScope.authorizeMsg= response.data.message;
          }
          //for closing all dialogs on error

          $location.path('/login');


          }
          return $q.reject(response);

        }
      };
      } ]).config(function($routeProvider,$locationProvider, $httpProvider) 
{	
	
	$routeProvider
        .when('/login', 
        {
        	controller	: 'loginController',
            templateUrl	: 'html/login.html'
        })
        .when('/home', 
        {
        	controller	: 'homeController',
            templateUrl	: 'html/home.html'
        })
        .when('/sqoop', 
        {
        	controller	: 'sqoopController',
            templateUrl	: 'html/sqoop.html'
        })
         .when('/sqoopDetails', 
        {
        	controller	: 'sqoopDetailController',
            templateUrl	: 'html/sqoopDetail.html'
        })
         .when('/addRecords/:ingestionSetId', 
        {
        	controller	: 'addRecordController',
            templateUrl	: 'html/addRecord.html'
        })
          .when('/updateRecords', 
        {
        	controller	: 'updateRecordController',
            templateUrl	: 'html/updateRecod.html'
        })
         .when('/updateFileRecords', 
        {
        	controller	: 'updateFileRecordController',
            templateUrl	: 'html/updateFileRecord.html'
        })
           .when('/uploadRecords/:ingestionSetId', 
        {
        	controller	: 'uploadController',
            templateUrl	: 'html/upload.html'
        })
        .when('/file', 
        {
        	controller	: 'fileController',
            templateUrl	: 'html/file.html'
        })
        .when('/fileDetails', 
        {
        	controller	: 'fileDetailController',
            templateUrl	: 'html/fileDetail.html'
        })
         .when('/fileAddRecords/:ingestionSetId', 
        {
        	controller	: 'fileAddRecordController',
            templateUrl	: 'html/fileAddRecord.html'
        })
         .when('/fileUploadRecords/:ingestionSetId', 
        {
        	controller	: 'fileUploadRecordController',
            templateUrl	: 'html/fileUploadRecord.html'
        })
        .when('/track', 
        {
        	controller	: 'trackController',
            templateUrl	: 'html/track.html'
        })
        .when('/dashboard', 
        {
        	controller	: 'dashboardController',
            templateUrl	: 'html/dashboard.html'
        })
        .when('/projectDashboard', 
        {
        	controller	: '',
            templateUrl	: 'html/projectDashboard.html'
        })
        .when('/admin', 
        {
        	controller	: '',
            templateUrl	: 'html/admin.html'
        })
        .when('/logs', 
        {
        	controller	: '',
            templateUrl	: 'html/logs.html'
        })
        .when('/logout', 
        {
        	controller	: 'commonController',
        	templateUrl	: 'html/logout.html'
        })
        .when('/dataUpload', 
        {
        	controller	: 'dataUploadController',
        	templateUrl	: 'html/dataUpload.html'
        })
        .when('/helpLink', 
        {
        	controller	: 'homeController',
        	templateUrl	: 'html/helpLinkDocument.html'
        })
        .otherwise({
            redirectTo	: '/login'
        });
	    $httpProvider.interceptors.push('httpInterceptor');
	//$httpProvider.interceptors.push('loginInterceptor');
}).factory('noCacheInterceptor', function () {
    return {
        request: function (config) {
            //console.log(config);
            if (config.method.toUpperCase() === "GET" || config.method.toUpperCase() === "POST") {
                if (config.url.indexOf('.html') === -1) {
                    var seperator = config.url.indexOf('?') === -1 ? '?' : '&';
                    config.url = config.url + seperator + 'ts=' + new Date().getTime();
                }
            }
            return config;
        }
    };
})
