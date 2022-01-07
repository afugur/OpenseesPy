# Units are N,mm
# Author Ahmet Furkan UĞUR
# email : afurkanugur05@gmail.com

from openseespy.opensees import *

wipe()

# Create Model Builder (with two-dimensions and 2 DOF/node)

model("basic","-ndm",2,"-ndf",3)

# Create Nodes
#-----------------------------

# Set model geometry

width = 6000 # mm
height = 6300 # mm

# Create Nodes
# tag, X, Y

node(1,0,height/2)
node(2,0,height)
node(3,width,height)
node(4,width,height/2)
node(5,0,0)
node(6,width,0)

# Fix support at base of columns

fix(5,1,1,1)
fix(6,1,1,1)

# Define materials

# Concrete C25

fpc = -25
fpcu = fpc * 0.2
epsco = -0.002
epsu = epsco * 10
lamba = 0.2
ft = -fpc/25
Ets = 2 * fpc/(epsco * 20)

uniaxialMaterial('Concrete02', 1, fpc, epsco, fpcu, epsu, lamba, ft, Ets)

# Steel S450

Fy = 450
Eo = 200000
b = 0.02

uniaxialMaterial('Steel01',2,Fy,Eo,b)

# Define cross-section

colWidth = 300 # mm
colDepth = 300 # mm

beamWidth = 250 # mm
beamDepth = 300 # mm

# As = (π * r2) * n
# n = number of rebars

As1 = 804/4 # mm2
As2 = 452/4 # mm2 


cover = 30 # mm


# some variables derived from the parameters
y1 = colDepth / 2.0
z1 = colWidth / 2.0

section('Fiber', 1)

#---------- COLUMN -------------------

# Create the concrete core fibers
patch('rect', 1, 10, 1, cover - y1, cover - z1, y1 - cover, z1 - cover)

# Create the concrete cover fibers
patch('rect', 1, 10, 1, -y1, z1 - cover, y1, z1)
patch('rect', 1, 10, 1, -y1, -z1, y1, cover - z1)
patch('rect', 1, 2, 1, -y1, cover - z1, cover - y1, z1 - cover)
patch('rect', 1, 2, 1, y1 - cover, cover - z1, y1, z1 - cover)

# Create Fibers

layer('straight', 2, 2, As1, y1 - cover, z1 - cover, y1 - cover, cover - z1)
layer('straight', 2, 2, As1, cover - y1, z1 - cover, cover - y1, cover - z1)



# ----------- BEAM ---------------------------------

section('Fiber', 2)

y2 = beamDepth / 2.0
z2 = beamWidth / 2.0

# Create the concrete core fibers
patch('rect', 1, 10, 1, cover - y2, cover - z2, y2 - cover, z2 - cover)

# Create the concrete cover fibers 
patch('rect', 1, 10, 1, -y2, z2 - cover, y2, z2)
patch('rect', 1, 10, 1, -y2, -z2, y2, cover - z2)
patch('rect', 1, 2, 1, -y2, cover - z2, cover - y2, z2 - cover)
patch('rect', 1, 2, 1, y2 - cover, cover - z2, y2, z2 - cover)

# Create Fibers

layer('straight', 2, 2, As2, y2 - cover, z2 - cover, y2 - cover, cover - z2)
layer('straight', 2, 2, As2, cover - y2, z2 - cover, cover - y2, cover - z2)

# ------------- Create Element ---------------

geomTransf('Linear', 1)

# Column

A_Col = colWidth * colDepth # Area
I_Col = (colWidth * colDepth**3)/12 # Inertia
E_Modulus = 31000

element('elasticBeamColumn', 1, 5, 1, A_Col, E_Modulus, I_Col, 1)
element('elasticBeamColumn', 2, 1, 2, A_Col, E_Modulus, I_Col, 1)
element('elasticBeamColumn', 3, 6, 4, A_Col, E_Modulus, I_Col, 1)
element('elasticBeamColumn', 4, 4, 3, A_Col, E_Modulus, I_Col, 1)

# Beam

A_Beam = colWidth * colDepth # Area
I_Beam = (colWidth * colDepth**3)/12 # Inertia
E_Modulus = 31000


element('elasticBeamColumn', 5, 2, 3, A_Beam, E_Modulus, I_Beam, 1)

# ------ Force ------


#  a parameter for the axial load

fcd = 25 / 1.5
Ac = (300)*(300)

fyd = 450 / 1.15
Ast = 804

Pu = 0.85 * fcd * Ac + Ast * fyd
# 10% of axial capacity of columns
P = Pu / 10



# Create a Plain load pattern with a Linear TimeSeries
timeSeries('Linear', 1)
pattern('Plain', 1, 1)


# Create nodal loads at nodes 3 & 4
#    nd  FX,  FY, MZ
load(2, 0.0, -P, 0.0)
load(3, 0.0, -P, 0.0)

# ------------------------------
# Start of analysis generation
# ------------------------------

# Create the system of equation, a sparse solver with partial pivoting
system('BandGeneral')

# Create the constraint handler, the transformation method
constraints('Transformation')

# Create the DOF numberer, the reverse Cuthill-McKee algorithm
numberer('RCM')

# Create the convergence test, the norm of the residual with a tolerance of
# 1e-12 and a max number of iterations of 10
test('NormDispIncr', 1.0e-12, 10, 3)

# Create the solution algorithm, a Newton-Raphson algorithm
algorithm('Newton')

# Create the integration scheme, the LoadControl scheme using steps of 0.1
integrator('LoadControl', 0.1)

# Create the analysis object
analysis('Static')

# ------------------------------
# End of analysis generation
# ------------------------------


# ------------------------------
# Finally perform the analysis
# ------------------------------

# perform the gravity load analysis, requires 10 steps to reach the load level
analyze(10)

# Print out the state of nodes 3 and 4
# print node 3 4

# Print out the state of element 1
# print ele 1

u3 = nodeDisp(1, 2)
u4 = nodeDisp(2, 2)

u3 = round(u3,3)
u4 = round(u4,3)

print("Z1 = " + str(u3) + " mm")

print("Z2 = " + str(u4) + " mm")
