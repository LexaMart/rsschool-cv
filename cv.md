Alex, Martinkevich Phone: +375298761617, Email: LexaMart1708@gmail.com I would like to improve my coding skills, to get acquainted with interesting people and share with them my thoughts. For me it's really important to be envolved in education progress in order to help others with they problems Python, c++, java. from vpython import sphere, vector, color, rotate from threading import Thread import math from random import randint, choice class TheSamePlanetsException: def str(self): return repr("Your planets have the same coordinates")
class WrongCoordinatesException(Exception): def str(self): return repr("Wrong coordinates of your planet")

class WrongNumberException(Exception): def str(self): return repr("Number of planets can't be negative")

class WrongRadiusException(Exception): def str(self): return repr("Radius can't be negative")

class WrongMassException(Exception): def str(self): return repr("Mass can't be negative")

class Engine: def init(self, num, step, file, mod): if not mod: self.star = Object(1.9885e30, 1) self.n = num self.step = step self.planets = self.list_creating() else: self.radius_mod = None self.mass_mod = None self.planet_speed_x_float = None self.planet_speed_y_float = None self.planet_masses_float = None self.file_path = file self.finish_coordinates_x = [] self.finish_coordinates_y = [] self.planets = [] self.read_from_file(file) self.mod = mod self.gravity_const = 6.667e-11 self.run()

def read_from_file(self, file_path):
    """
    Definition reads data from file "LOAD", transforms all from 'string' to 'integer' or 'float'
    and creates list with planets of class 'Moving_object'
    """
    try:
        with open(file_path, "r") as file:
            for line in file:
                if line.startswith("Steps : "):
                    step = float(line[8:-1])

                elif line.startswith("Mass of central object: "):
                    mass = line[24:-1]
                    mass_mod = float(mass)
                    if mass_mod < 0:
                        raise WrongMassException

                elif line.startswith("Radius of central object: "):
                    radius_mod = float(line[26:-1])
                    if radius_mod < 0:
                        raise WrongRadiusException

                elif line.startswith("Number of planets: "):
                    num = int(line[19:-1])
                    if num < 0:
                        raise WrongNumberException

                elif line.startswith("Masses of planets: "):
                    planet_masses = line[20:-2].split(", ")
                    planet_masses_float = []
                    for element in planet_masses:
                        element_float = float(element)
                        planet_masses_float.append(element_float)

                elif line.startswith("Coordinate y : "):
                    planet_y = line[16:-2].split(", ")
                    planet_y_float = []
                    for element in planet_y:
                        element_float = float(element)
                        planet_y_float.append(element_float)

                elif line.startswith("Coordinate x : "):
                    planet_x = line[16:-2].split(", ")
                    planet_x_float = []
                    for element in planet_x:
                        element_float = float(element)
                        planet_x_float.append(element_float)

                elif line.startswith("Coordinate Speed x : "):
                    planet_speed_x = line[22:-2].split(", ")
                    planet_speed_x_float = []
                    for element in planet_speed_x:
                        element_float = float(element)
                        planet_speed_x_float.append(element_float)

                elif line.startswith("Coordinate Speed y : "):
                    planet_speed_y = line[22:-2].split(", ")
                    planet_speed_y_float = []
                    for element in planet_speed_y:
                        element_float = float(element)
                        planet_speed_y_float.append(element_float)
    except IOError:
        raise

    for index in range(0, num):
        self.planets.append(Moving_object(planet_masses_float[index], planet_speed_x_float[index],
                                          planet_speed_y_float[index], planet_x_float[index],
                                          planet_y_float[index]))
        if radius_mod * -1 <= self.planets[index].pos_x <= radius_mod \
                and radius_mod * -1 <= self.planets[index].pos_y <= radius_mod:
            raise WrongCoordinatesException

    self.n = num
    self.star = Object(mass_mod, radius_mod)
    self.step = step
    self.radius_mod = radius_mod
    self.mass_mod = mass_mod
    self.planet_masses_float = planet_masses_float
    self.planet_speed_x_float = planet_speed_x_float
    self.planet_speed_y_float = planet_speed_y_float
    file.close()

def list_creating(self):
    """
     num: number of planets
     Definition makes list of random planets(Class: "Moving_obj) with random parameters
    """
    planets = []
    for i in range(0, self.n):
        planets.append(Moving_object(randint(1.25e20, 1.8986e27), randint(0, 9), randint(0, 9),
                                     choice((-1, 1)) * randint(2, 15), choice((-1, 1)) * randint(0, 15)))
    return planets

def write_to_file(self):
    """
    Definition rewrites file 'LOAD.txt' with new coordinates
    """
    file = open(self.file_path, "w")
    file.write(f'Steps : {self.step}\nMass of central object: {self.mass_mod}\n'
               f'Radius of central object: {self.radius_mod}\nNumber of planets: {self.n}\n'
               f'Masses of planets: {self.planet_masses_float}\nCoordinate y : {self.finish_coordinates_y}\n'
               f'Coordinate x : {self.finish_coordinates_x}\nCoordinate Speed x : {self.planet_speed_x_float}\n'
               f'Coordinate Speed y : {self.planet_speed_y_float}')
    file.close()

def run(self):
    """
    Definition draws all planets and their orbits at the same time
    """
    self.star.draw_star_sphere()
    for index in range(0, self.n):
        thread = Thread(target=self.drawing, args=(self.planets[index],))
        thread.start()

def drawing(self, planet):
    """
    planet: object of class 'Moving_obj'
    Definition draws moving simulation in gravity field and makes list with finishing coordinates for "MOD"
    """
    dt = 100  # step = 100 seconds
    gravity_power = self.gravity_const * self.star.mass * planet.mass / (planet.get_distance() ** 2)
    ang_vel = math.sqrt(gravity_power / (planet.mass * planet.get_distance()))
    theta_planet = ang_vel * dt * planet.get_moving_direction()
    planet_sphere = sphere(pos=vector(planet.pos_x, planet.pos_y, 0), color=color.blue, radius=0.25,
                           make_trail=True)
    iteration_number = 0
    while dt <= self.step * 86400:  # 1 day = 86400 seconds
        planet_sphere.pos = rotate(planet_sphere.pos, angle=theta_planet)
        dt += 100
        iteration_number += 1
    if self.mod:
        self.finish_coordinates_x.append(planet_sphere.pos.x)
        self.finish_coordinates_y.append(planet_sphere.pos.y)
        self.write_to_file()
class Object:

def __init__(self, mass, radius):
    self.radius = radius
    self.mass = mass

def draw_star_sphere(self):
    """
    Definition returns sphere of central object. "Vpython" requires it.
    """
    star_sphere = sphere(pos=vector(0, 0, 0), color=color.yellow, radius=self.radius)
    return star_sphere
class Moving_object:

def __init__(self, mass, speed_x, speed_y, position_x, position_y):
    self.mass = mass
    self.speed_x = speed_x
    self.speed_y = speed_y
    self.pos_x = position_x
    self.pos_y = position_y
    self.scaling_const = 4.98e10  # distance from Sun to Earth in metres

def get_distance(self):
    """
    Definition returns distance between the central object and planet in kilometres.
    """
    dist = math.sqrt((self.pos_x ** 2) + (self.pos_y ** 2)) * self.scaling_const
    return dist

def get_moving_direction(self):
    """
    Definition returns the direction of movement for the planets. It depends on the position of "moving_object"
    relative to the position of central object and vector of "speed"
    """

    if self.pos_x >= 0 < self.pos_y or self.pos_x > 0 >= self.pos_y:
        if self.pos_y > 0 and self.speed_y <= self.pos_y or self.pos_y <= 0 and self.speed_x <= 0:
            return -1
    return 1
draw = Engine(num=10, step=365, file="LOAD.txt", mod=False) CodeWars, Meetings, Tests, Projekts Warsaw University of Technologies, Belarusian State University B2, Model united nations, travelling
