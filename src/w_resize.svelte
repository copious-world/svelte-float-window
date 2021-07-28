<script>

import { createEventDispatcher } from 'svelte';
const dispatch = createEventDispatcher();

let offset = { x: 0, y: 0 };

function start_drag(e) {
    let mover = e.target
    if ( mover ) {
        offset.x = e.pageX;;
        offset.y = e.pageY;
        window.addEventListener('mousemove', dragging, true);
        window.addEventListener('mouseup', stop_drag, true);
    }
}

function stop_drag(e) {
    window.removeEventListener('mousemove', dragging, true);
    window.removeEventListener('mouseup', stop_drag, true);
    dispatch('message', {
        "cmd" : "stop"
    });
}


function dragging(e) {
    let mover = e.target
    if ( mover === undefined ) return

    const width_delta = (e.pageX - offset.x);
    const height_delta = (e.pageY - offset.y)
    dispatch('message', {
        "cmd" : "drag",
        "w_delta": width_delta,
        "h_delta": height_delta
    });

}



// https://medium.com/the-z/making-a-resizable-div-in-js-is-not-easy-as-you-think-bda19a1bc53d

</script>
    <div class="drag-container" on:mousedown={start_drag}  on:mouseup={stop_drag} >
        <slot></slot>
    </div>    

<style>

    .drag-container {
        width: 25px;
        height: 25px;
        text-align: center;
        vertical-align: middle;
        border-radius: 50%; /*magic to turn square into circle*/
        background: white;
        border: 3px solid #4286f4;
        box-sizing: border-box;
        position:absolute;
        right: -5px;
        bottom: -5px;
        cursor: nwse-resize;
    }


</style>