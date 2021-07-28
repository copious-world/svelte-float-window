<script>

	import Resizer from './w_resize.svelte'
	import { onMount } from 'svelte';

	export let title;
	export let scale_size_array;
	export let index = 0;

	var SCROLL_WIDTH = 24;
	var Z_BOTTOM = 500
	const minimum_size = 300;
	// 
	//-- let the popup make draggable & movable.
	let offset = { x: 0, y: 0 };

	let original_width = 0;
	let original_height = 0;
	let original_x = 0;
	let original_y = 0;


	function originals(popup) {
		if ( popup ) {
			original_width = parseFloat(getComputedStyle(popup, null).getPropertyValue('width').replace('px', ''));
			original_height = parseFloat(getComputedStyle(popup, null).getPropertyValue('height').replace('px', ''));
			original_x = popup.getBoundingClientRect().left;
			original_y = popup.getBoundingClientRect().top;
		}
	}


	let scale_current_size = Object.assign({},scale_size_array)

	onMount(() => {
		let z_index_buffer = window._fws_z_order
		if ( z_index_buffer === undefined ) {
			window._fws_z_order = []
			z_index_buffer = window._fws_z_order
		}
		z_index_buffer.push(index)

		//
		var popup = document.getElementById(`popup_${index}`);
		if ( popup === undefined ) return
		do_resize(index)
		//
		var popup_bar = document.getElementById(`popup_bar_${index}`);
		//
		popup_bar.addEventListener('mousedown', mouseDown, false);
		window.addEventListener('mouseup', mouseUp, false);
		window.addEventListener('keydown',(e) => {
			if ( e.keyCode == 27 ) { // if ESC key pressed
				var popup = document.getElementById(`popup_${index}`);
				if ( popup === undefined ) return
				popup.style.display = "none";
			}
		})

		window.addEventListener("resize",(e) => {
			do_resize(index)
		})

		window.start_floating_window = (index) => {
			popup_starter(index)
		}

	})


	function fix_z_order(idx) {
		let z_index_buffer = window._fws_z_order
		if ( z_index_buffer !== undefined ) {
			if ( typeof idx === "string" ) idx = parseInt(idx)
			let pos = z_index_buffer.indexOf(idx)
			if ( pos >=0 ) {
				z_index_buffer.splice(pos,1)
				z_index_buffer.push(idx)
				let bottom_z = Z_BOTTOM
				for ( let p_i of z_index_buffer ) {
					let popupId = `popup_${p_i}`
					let popup = document.getElementById(popupId);
					if ( popup ) {
						popup.style.zIndex = bottom_z++
					}
				}

			}
		}
	}

	function handle_close(e) {
		let closer = e.target.id
		if ( closer ) {
			let idx = closer.replace('btn_close_','')
			let popupId = `popup_${idx}`
			var popup = document.getElementById(popupId);
			if ( popup === undefined ) return
			popup.style.display = "none";
		}
	}



	function fix_height(ii,startup_delta) {
		let txt_area = document.getElementById('blg-window-full-text');
		if ( txt_area ) {
			//
			txt_area._blg_app_resized = true
			//
			let r = txt_area.getBoundingClientRect();
			//
			var popup = document.getElementById(`popup_${ii}`);
			if ( popup ) {
				let rp = popup.getBoundingClientRect();
				let h = Math.floor(rp.bottom - r.top) - startup_delta
				txt_area.style.height = h + "px";
			}
		}
	}


	function handle_size(event) {
		if ( event && event.detail ) {
			if ( event.detail.cmd == 'drag' ) {
				let w_dlt = event.detail.w_delta
				let h_dlt = event.detail.h_delta
				//
				const width = original_width + w_dlt;
				const height = original_height + h_dlt;
				var popup = document.getElementById(`popup_${index}`);
				if ( popup ) {
					if (width > minimum_size) {
						popup.style.width = width + 'px'
					}
					if (height > minimum_size) {
						popup.style.height = height + 'px'
					}
				}
			} else if ( event.detail.cmd == 'stop' ) {
				var popup = document.getElementById(`popup_${index}`);
				originals(popup)
			}
		}
	}

	function do_resize(ii) {
		var popup = document.getElementById(`popup_${ii}`);
		if ( popup ) {
			popup.style.width = (window.innerWidth*scale_current_size.w) - SCROLL_WIDTH + "px";
			popup.style.height = (window.innerHeight*scale_current_size.h) - SCROLL_WIDTH + "px";
			originals(popup)
		}
	}

	function popup_starter(ii) {
		var popup = document.getElementById(`popup_${ii}`);
		if ( popup && (popup.style.display !== 'block') ) {
			popup.style.top = "4px";
			popup.style.left = "4px";
			popup.style.width = (window.innerWidth*scale_current_size.w) - SCROLL_WIDTH + "px";
			popup.style.height = (window.innerHeight*scale_current_size.h) - SCROLL_WIDTH + "px";
			popup.style.display = "block";
			setTimeout(() => { fix_height(ii,4); },40)
		}
		setTimeout(() => { fix_height(ii,4); },20)
	}

	function mouseUp(e) {  // popupMove is temporarily assiged to just one subwindow
		window.removeEventListener('mousemove', popupMove, true);
	}

	function mouseDown(e) {
		let mover = e.target.id
		if ( mover ) {
			let idx = mover.replace('popup_bar_','')
			let popupId = `popup_${idx}`
			var popup = document.getElementById(popupId);
			if ( !(popup) ) return
			fix_z_order(idx)
			offset.x = e.clientX - popup.offsetLeft;
			offset.y = e.clientY - popup.offsetTop;
			window.addEventListener('mousemove', popupMove, true);
		}
	}

	function popupMove(e){
		var popup = document.getElementById(`popup_${index}`);
		if ( popup === undefined ) return
		popup.style.position = 'absolute';
		var top = Math.max((e.clientY - offset.y),0);
		var left = Math.max((e.clientX - offset.x),0);
		popup.style.top = top + 'px';
		popup.style.left = left + 'px';
	}
	//-- / let the popup make draggable & movable.

</script>

<div id="popup_{index}"  class="popup" >
	<div id="popup_bar_{index}" class="popup_bar" on:mousedown={mouseDown} on:mouseup={mouseUp} >
	  {title}
	  <span id="btn_close_{index}" class="btn_close" on:click={handle_close} >[X]</span>
	</div>
	<p style="font-size:0.75em;font-weight:bold;color:darkgreen;padding-left:4px">Press ESC to close window.</p>
		<slot></slot>
	<Resizer on:message={handle_size} >&#9449;</Resizer>
</div>

<style>

	.popup {
		display:none;
		background-color:rgb(255, 255, 255);
		position:absolute;
		top:0px;
		z-index:9999;
		box-shadow: 6px 6px 5px #888888;
		border-radius:6px;
		border:1px solid #4f4f4f;
		width: 40%
	}

	.popup_bar {
		width:100%;
		background-color:#76d825;
		position:relative;top:0;
		border-radius:6px 6px 0 0;
		text-align:center;
		height:24px;
		cursor:move
	}

	.btn_close {
		float:right;
		cursor:pointer;
		padding-right:6px;
	}

</style>