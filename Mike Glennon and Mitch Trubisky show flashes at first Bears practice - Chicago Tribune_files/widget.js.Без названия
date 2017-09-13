trb.runInfuse.push(function() {
var tries = 0;
var related = $('.trb_ar_rail div[data-eg-type=related]');
var base = '.troncdata.com/';
if (location.pathname.match(/^\/hoy\//)) {
  base = 'ct-hoyrecommend' + base;
} else if (location.host.match(/chicago/)) {
  base = 'chirecommend' + base;
} else if (location.host.match(/baltimoresun/)) {
  base = 'bsrecommend' + base;
} else if (location.host.match(/sun-sentinel/)) {
  base = 'flrecommend' + base;
} else if (location.host.match(/southflorida/)) {
  base = 'sfrecommend' + base;
} else {
  base = 'recommend' + base;
}
if (location.search.indexOf('insecure') != -1) {
  base = 'http://' + base;
} else {
  base = 'https://' + base;
}
var params = [];
var isShown = false;
var isIdFixed = false;
var dest = window.location.href;
function showRecommendations() {
  var once = false;
  $('.trb_ar_rail div[data-eg-type=related] ul.trb_eg_u a').click(function(e) {
    e.preventDefault();
    if (!once) {
        once = true;
        var link = this;
        var target = this.href.split('/').pop().replace('#nt=', '|');
        var p = ['f', userid, 'click', target]
        $.ajax(base + p.join('/'), {
            'crossDomain': true,
            'complete': function () {
                window.location = link.href
              }
          }
        );
    }
  });

  related.css('opacity', 1);
  isShown = true;
  var tmpid = localStorage.getItem('recsys_id');
  var userid = params[0];
  if (!isIdFixed && tmpid && tmpid != userid) {
    isIdFixed = true;
    var p = ['f', userid, 'temp_id', tmpid];
    $.ajax(base + p.join('/'), {
        'crossDomain': true,
        'error': function () { isIdFixed = false; },
        'success': function () { localStorage.setItem('recsys_id', userid); }
    });
  }
}

var control = true
function timedOut() {
  if (!isShown && !control) $.ajax(base + ['t'].concat(params).join('/'));
  showRecommendations();
}

function waitForData() {
  if(trb.hasOwnProperty('data')
     && trb.data.hasOwnProperty('page')
     && trb.data.page.hasOwnProperty('slug')
    ){
    (function($) {
      var a = location.hash.match(/#troncrecs-(.+)/)
      if (a) {
        localStorage.setItem('recsys_override', a[1] + '--' + trb.cookie.get('uuid'));
      }
      var tmpId = localStorage.getItem('recsys_id');
      var userId = localStorage.getItem('recsys_override') || trb.cookie.get('uuid') || tmpId;
      if (!userId) {
        var userId = Date.now() +''+ Math.random()
        localStorage.setItem('recsys_id', userId);
      }


      params = [userId, trb.data.page.slug];
      var once = false;
      function sendImp(i) {
        if (!once) {
          once = true;
          $.ajax(base + ['i'].concat(params, [i]).join('/'));
        }
      }
      var t = new Date();
      var query = '?'
        + 'geo_time_zone=' + t.getTimezoneOffset() * -60 + '&'
        + 'time_iso8601=' + t.toISOString() + '&'
        + 'url=' + location.href.split('#')[0] + '&'
        + 'section=' + location.pathname.split('/')[1] + '&';

      if (trb.data.hasOwnProperty('metrics')
         && trb.data.metrics.hasOwnProperty('s')) {
        query = query + 'signed_in_status=' + trb.data.metrics.s.prop62 + '&';
      }
      if (trb.data.isMobile) {
	    query = query + 'user_agent_os_family=iOS-Android&';
      }
      var unset_tmpId = false;
      if (tmpId && userId != tmpId) {
        query = query + 'tmp_id=' + tmpId + '&';
        unset_tmpId = true;
      }

      $.ajax(base + 'recommendations/' + params.join('/') + query, {
        'dataType': 'json',
        'crossDomain': true,
        'complete': showRecommendations,
        'error': timedOut,
        'success': function(data) {
                     if (unset_tmpId) {
                       localStorage.removeItem('recsys_id');
                     }

                     if (data['markup'] && !isShown) {
                       control = false;
                       related.replaceWith(data['markup']);
                       sendImp(data['recsys']);
                     } else {
                       sendImp('related');
                     }
                   }
      });
      setTimeout(timedOut, 2500);
    })(i$.jQuery);
  } else if (tries < 10) {
    tries = tries + 1;
    setTimeout(waitForData,50);
  } else {
    timedOut();
  }
}
waitForData();
});
