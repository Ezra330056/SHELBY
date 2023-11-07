"use strict";

$.fn.extend(
{ 
    modal : function( options) 
    {
        
        if (typeof options === 'number') {
            console.error(typeof(options), '- wrong type for modal');
            return false;
        }
         
        if ($(this).length > 1) {
            console.error('Call only on modal at once!', self); 
            return false;
        }
        
        if (typeof options !== 'object'){
            options = {'method' : options};
        }

        var defaults = 
        {
            cache: false,
            closeOnBackdrop : true,
            closeOnEscape   : true,
            submitOnEnter   : false,
            closeOnSubmit   : false,
            autoFocus       : false,
            origin          : '',
            method          : '',
            container       : $('#wrapper'),
            backdrop        : '<div class="bxmodal-backdrop" />',
            loadRequest     : '<div class="loadRequest" />',
            openClass       : 'bxmodal-open',
            multiClass      : 'bxmodal-multi',
            close           : '[data-dismiss="modal"]',
            submit          : '[data-submit="modal"]',
            ajax            : false,
            ajaxData        : {},
            closeMethod     : 'close',
            multi           : {
                source      : null,
                active      : false
            }
        }
            
        var o        = $.extend(defaults, options);
        var self     = $(this), 
            backdrop = $(o.backdrop), 
            body     = $('body'), 
            modal    = {
                request   : false,
                keyEscape : 27,
                keyEnter  : 13
            }, 
            doc      = $(document), 
            load;

        var events = {
            init : function() 
            {
                self.trigger('init', self);
                
                if (typeof(o.onInit) == 'function')
                   o.onInit (self);   
            },
            show : function()
            {
                self.trigger('show', self);
                
                if (typeof(o.onShow) == 'function')
                   o.onShow (self);  
            },
            shown: function()
            {               
                self.trigger('shown', self);

                if (typeof o.onShown  === 'function') 
                    o.onShown (self);  
            },
            hide : function()
            {
                self.trigger('hide', self);
            
                if (typeof o.onHide === 'function')
                    o.onHide(self);
            },
            submit : function()
            {
                self.trigger('submit', self);

                if (typeof o.onSubmit === 'function') {
                    var retVal = o.onSubmit(self); 
                    
                    if(o.closeOnSubmit && retVal != false) {
                        modal.hide(); 
                    }
                }
            },
            hidden : function()
            {           
                self.trigger('hidden', self);
                
                if (typeof after=== 'function')
                    after(self);
                    
                if (typeof o.onHidden === 'function')
                    o.onHidden(self);
            },
            cancel : function() {
                self.trigger('cancel', self); 
                
                if (typeof o.onCancel  === 'function')
                    o.onCancel (self);    
            }
        };
        
        modal.init = function()
        {
            
            if (self.length == 0 && !o.ajax) {
                console.error('Modal not found! Maybe wrong id?'); 
                return false;
            }

            if (body.hasClass(o.openClass) && o.method != 'swap') {
                if(body.hasClass(o.multiClass) && o.method != "hide" && o.method != "close") {
                    var first =  $('#' + body.attr('data-modal-open'));
                    var second = first.find('.modal');
                    console.error('Already two modals are open! Isn`t it enought?', first, second); 
                    return false;
                }
                
                var openModal = body.attr('data-modal-open');
                
                if (openModal) {
                    o.multi.active = true;
                    o.multi.source = $('#' + openModal); 
                    o.close       = '[data-dismiss="mini-modal"]';
                    o.submit      = '[data-submit ="mini-modal"]';
                }
            } 
            
            events.init();
    
            if (o.method == "hide" || o.method == "close"){
                self.trigger(o.closeMethod,  [o.onHidden]);

                return false;
            }
    
    
            if (o.ajax) {
                if   (o.cache && self.length > 0)
                      o.ajax = false;
                else  self = $('<div class="preloader" />');
            }
             
            if(o.method == 'swap') {
                                
                var old = $('#'+$(document.body).attr('data-modal-open'));
                old.trigger('swap-remove');
            };
                     
            modal.show();  
        };

        modal.swapRemove = function() 
        {
            modal.unbind();
            self.hide();
            
            if (typeof load != 'undefined' && load.length > 0) {
                load.remove();
                self.removeClass('load');
            }
            
            events.hidden();
            self.removeClass('show').appendTo(body);
        
        };
        
        
        modal.getScrollWidth = function()
        {
            var width = 0;

            if (o.container.height() > $(window).height()) {
                body.css('overflow' ,'scroll');
                width = body.width();

                body.css('overflow' ,'hidden');
                width = body.width() - width; 
            }

            return width;
        };

        modal.setCenter = function()
        {
            var top, margin;
            margin     = parseInt(self.css('marginTop').slice(0,-2));
            margin     = $.isNumeric(margin) ? margin : 0;
            top        = parseInt((backdrop.height()/ 2)  - (self.outerHeight() / 2) - margin);
            top        = top > 0 ? top : 0;
            
            self.css("top", (top + "px"));

        };
        
        modal.handleEvent = function (event) 
        {
            event.preventDefault();
            event.stopPropagation();
        };
        
        modal.remove = function()
        {
            self
            .removeClass('show')
            .unwrap(backdrop);
            
            if (o.multi.active) {
                self.appendTo(body);
            }  
            
        };

        
        modal.keyPress = function(event)
        {
            if(event) modal.handleEvent(event);
            
            if (o.closeOnEscape) {
                if (event.keyCode == modal.keyEscape && !modal.request)  {    
                    event.preventDefault();
                    modal.hide(); 
                    events.cancel();
                } 
            }

            if (o.submitOnEnter) {
                if(event.keyCode == 13) {
                    modal.submit();
                } 
            }

        };
        
        modal.startLoad = function(event)
        {
            if(event) modal.handleEvent(event);
            
            modal.request = true;
            load =  $(o.loadRequest).hide();
            self.addClass('load');
            
            if (self.hasClass('large')) {
                var scrollTop = self.get(0).scrollTop;
                load.css('top',scrollTop).show();
            }

            self.append(load); 
            load.fadeIn('fast');
        };
        
        modal.stopLoad = function(event)
        {
            if(event) modal.handleEvent(event);
            
            if (typeof load != 'undefined' && load.length > 0) {
                modal.request = false;
                self.removeClass('load');
                load.fadeOut('fast', function() 
                {
                    $(this).remove();
                });
            }
        };
        
        modal.submit   = function(event)
        {
            if(event) modal.handleEvent(event);
            
            events.submit();
        };
        
        modal.magicSwap   = function(event,data)
        {
            modal.unbind();
            
            self
            .removeClass('show')
            .trigger('hidden')
            .appendTo(body);
            
            self = $(data);
                            
            self.appendTo(backdrop);
            modal.setCenter();
            modal.bind ();
            
            self.addClass('show').fadeIn('fast', function()
            {
                self.trigger('shown');    
            });
        };
        
        modal.bind = function()
        { 
            self
            .on('resize',            modal.setCenter)
            .on(o.closeMethod,       modal.hide)
            .on('magic-swap',        modal.magicSwap)
            .on('start-request',     modal.startLoad)
            .on('stop-request',      modal.stopLoad)
            .on('swap-remove',       modal.swapRemove)
            .on('click',   o.submit, modal.submit)
            .on('click',   o.close, function (event)
            {
                 if(event) modal.handleEvent(event);
                 modal.hide();
                 events.cancel();
                 return false;
            });
            
            
            if (o.closeOnEscape || o.submitOnEnter) doc.on('keyup',   modal.keyPress);
            if (o.closeOnBackdrop) {
                backdrop.on('click', function(e)
                {
                    if (e.target ==  backdrop.get(0) && !modal.request)
                    {
                        modal.hide();
                        events.cancel();
                        return false;
                    }
                }); 
            }
            
            $(window).on('resize', modal.setCenter);
        };
        
        modal.unbind = function ()
        {
            if (o.closeOnEscape || o.submitOnEnter) doc.off('keyup',   modal.keyPress);
            if (o.closeOnBackdrop)  backdrop .off('click',  modal.hide);


            self
            .off(o.closeMethod,    modal.hide)
            .off('resize',        modal.setCenter)
            .off('click',         o.close)
            .off('magic-swap',    modal.magicSwap)
            .off('start-request', modal.startLoad)
            .off('swap-remove',   modal.swapRemove)
            .off('click',         o.submit, modal.submit)
            .off('stop-request',  modal.stopLoad);  
            
            $(window).off('resize', modal.setCenter);
        };
        
        modal.show = function ()
        {
            
            if (!o.ajax)  {
                modal.bind();
                events.show();
            }
            if (o.method == 'swap') {               

                backdrop = $(document.body).find('.bxmodal-backdrop');
            }
            
            self.appendTo(backdrop);

            
            if (o.multi.active) {
                o.multi.source.append(backdrop); 
                body.addClass(o.multiClass);
            } else if (o.method != 'swap') {
                body
                .addClass(o.openClass)
                .css('margin-right', modal.getScrollWidth() + 'px' )
                .append(backdrop);
                
                $('#header-container').css('left', '-'+ (modal.getScrollWidth() / 2) + 'px' );
            }
            
            if (o.multi.active) {
                var scrollTop = o.multi.source.get(0).scrollTop;
                backdrop.css('top',scrollTop);
            }
            
            if (o.method != 'swap') {
                backdrop.hide().fadeIn('fast', function()
                {
                    modal.afterDrop();
                });
            } else {
               modal.afterDrop(); 
            }
            
        };
        
        modal.afterDrop = function() 
        {
            if (o.ajax) {  
    
                $.get(o.ajax, o.ajaxData,  function(data)
                {
                    if (typeof data === 'object'){
                        if (typeof o.onAjaxError  === 'function')
                            o.onAjaxError (data); 
                            
                        modal.hide();
                        return false;
                    }
                    
                    self.fadeOut('slow', function()
                    {
                        self.remove();  
                        //self = $(data);
                        self = $(data).filter('div');
                        
                        modal.bind();
                        events.show();
                        self.appendTo(backdrop);
                        modal.effect();
    
                    });
                }); 
            } else {
                modal.effect();
            }
        };
        
        modal.effect = function()
        {
            modal.setCenter();
            
            if (!o.multi.active) {
               body.attr('data-modal-open', self.attr('id')); 
            }

            self.addClass('show').hide().fadeIn('fast');


            self.promise().done(function() 
            {
                if (o.autoFocus) {
                    self.find('[autofocus]').focus();
                }
                
                 events.shown(); 
            });
            
        };
        

        
        modal.hide = function(event, after)
        {
            if(event) modal.handleEvent(event);
            
            modal.request = false;
            events.hide();
            modal.unbind();
            
            if(self.length>1) console.log("Warning fetched more than one container for modal, length is "+self.length);

            self.fadeOut('fast', function()
            {
                if (typeof load != 'undefined' && load.length > 0) {
                    load.remove();
                    self.removeClass('load');
                }

                backdrop.fadeOut('fast', function()
                {

                    if(o.multi.active) {
                        body.removeClass(o.multiClass);
                    } else {
                        
                        body
                        .removeAttr('data-modal-open')
                        .removeClass(o.openClass)
                        .css('margin-right', '' );

                        $('#header-container').css('left', '');
                        document.body.style.overflow = '';   
                    }
                    
                    modal   .remove();
                    backdrop.remove();
                    events.hidden();
                    
                    if (o.ajax && !o.cache){
                        self.remove();
                    }                 
                    
                });
                            
            });

        };
        modal.init();
    },
    
    submitModal : function(ajax, data, callback) 
    {
        var self = $(this);
        
        self.trigger('start-request');
        
        $.postJSON(ajax, data , function(res) 
        {  
            if(!bx.error.isError(res)) {
                self.one('hidden', function () 
                {
                     bx.alert.show(
                         res.message, 
                         res.success, 
                         res.time
                     ); 
                     
                }).modal('close');
                
                if (typeof callback == "function") {
                    callback(res);
                }

            } else {self.modal('close');}
        });  
    }
    

    
});
    