extends KinematicBody

#Comienzo General
var health_now : int = 20
var health_max : int = 20
var ammo : int = 30
var score :int = 0

#Fisicas
var move_speed : float = 5.0
var jump_force : float = 5.0
var gravity : float = 12.0

#Configuración de Camara
var min_look_angle : float = -90.0
var max_look_angle : float = 90.0
var camera_sens : float = 0.025


#Vectores
var vel = Vector3()
var mouse_delta = Vector2()

#Llamar Objetos/Camara
onready var camera = get_node("Camera_Orbit/Camera")

export (NodePath) var joystickRightPath
onready var joystickRight : VirtualJoystick = get_node(joystickRightPath)

func _ready():
#	Input.set_mouse_mode(Input.MOUSE_MODE_CAPTURED)#Esconder Mouse <--
	pass

#func _input(event: InputEvent):
#	if event is InputEventMouseMotion:
#		mouse_delta = event.relative

func _process(delta):
#	#Rotar la camara en el eje x
#	camera.rotation_degrees -= Vector3(rad2deg(mouse_delta.y), 0, 0) * camera_sens * delta
#
#	#Rotar el jugador en el eje Y
#	camera.rotation_degrees.x = clamp(camera.rotation_degrees.x, min_look_angle, max_look_angle)
#
#	#Rotar el jugador en el eje y
#	rotation_degrees -= Vector3(0, rad2deg(mouse_delta.x), 0) * camera_sens * delta
	
	#Reiniciar el mouse_delta
#	mouse_delta = Vector2()
	pass

func rotate_camera():
	#Rotar la camara en el eje x
	#camera.rotation_degrees -= Vector3(rad2deg(joystickRight.get_output().y), 0, 0) * camera_sens
	
	#Rotar el jugador en el eje Y
	#camera.rotation_degrees.x = clamp(joystickRight.get_output().x, min_look_angle, max_look_angle)
	
	#Rotar el jugador en el eje y
	rotation_degrees -= Vector3(0, rad2deg(joystickRight.get_output().x), 0) * camera_sens
	
	#Reiniciar el mouse_delta
	mouse_delta = Vector2()

func _physics_process(delta):
	rotate_camera()
	
	#Reiniciar la velocidad x z de cada cuadro
	vel.x = 0
	vel.z = 0
	
	var input = Vector2()
	
	#Input de movimiento
	if Input.is_action_pressed("up"):
		input.y -= 1
	if Input.is_action_pressed("down"):
		input.y += 1
	if Input.is_action_pressed("left"):
		input.x -= 1
	if Input.is_action_pressed("right"):
		input.x += 1
	
	input = input.normalized()
	
	var adelante = global_transform.basis.z
	var derecha = global_transform.basis.x
	
	#poner la velocidad
	vel.z = (adelante * input.y + derecha * input.x).z * move_speed
	vel.x = (adelante * input.y + derecha * input.x).x * move_speed
	
	vel.y -= gravity * delta
	
	vel = move_and_slide(vel, Vector3.UP)
	
	if Input.is_action_pressed("jump") and is_on_floor():
		vel.y = jump_force
