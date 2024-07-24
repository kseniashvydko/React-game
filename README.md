import { useEffect, useState } from "react";

import ScoreBoard from "./components/ScoreBoard";
import blueCandy from './images/blue.png'
import greenCandy from './images/green.png'
import pinkCandy from './images/pink_dog.png'
import redCandy from './images/red.png'
import whiteCandy from './images/white.png'
import yellowCandy from './images/yellow.png'
import blank from './images/blank.jpg'
const width = 8;
const candyColors = [
  blueCandy,
  greenCandy,
  pinkCandy,
  redCandy,
  whiteCandy,
  yellowCandy
]

const App = () => {

  const [currentColorArrangement, setCurrentColorArrangement] = useState([])
  const [squareBeingDragged, setSquireBeingDragged] = useState(null)
  const [squareBeingReplaced, setSquireBeingReplaced] = useState(null)
  const [scoreDisplay, setScoreDisplay] = useState(0)

  // eslint-disable-next-line react-hooks/exhaustive-deps
  const checkForColumnOfThree = () => {
    for (let i = 0; i <= 47; i++) {
      const columnOfThree = [i, i * width, i + width *2]
      const decidedColor = currentColorArrangement[i]
      const isBlank = currentColorArrangement[i] === blank

      if( columnOfThree.every(squery => currentColorArrangement[squery] === decidedColor && !isBlank)) {
        setScoreDisplay((score) => score + 3)
        columnOfThree.forEach(square => currentColorArrangement[square] = blank)
        return true
      }
    }
  }



  // eslint-disable-next-line react-hooks/exhaustive-deps
  const checkForRowOfThree = () => {
    for (let i = 0; i < 64; i++) {
      const rowOfThree = [i, i + 1, i +2]
      const decidedColor = currentColorArrangement[i]
      const notValid = [6, 7, 14, 15, 22, 23, 30, 31, 38, 39, 46, 47, 54, 55, 63, 64]
      const isBlank = currentColorArrangement[i] === blank

      if (notValid.includes(i)) continue

      if( rowOfThree.every(squery => currentColorArrangement[squery] === decidedColor && !isBlank)) {
        setScoreDisplay((score) => score + 3)
        rowOfThree.forEach(square => currentColorArrangement[square] = blank)
        return true
      }
    }
  }


  // eslint-disable-next-line react-hooks/exhaustive-deps
  const checkForColumnOfFour = () => {
    for (let i = 0; i <= 39; i++) {
      const columnOfFour = [i, i * width, i + width *2, i + width * 3]
      const decidedColor = currentColorArrangement[i]
      const isBlank = currentColorArrangement[i] === blank

      if( columnOfFour.every(squery => currentColorArrangement[squery] === decidedColor && !isBlank)) {
        setScoreDisplay((score) => score + 4)
        columnOfFour.forEach(square => currentColorArrangement[square] = blank)
        return true
      }
    }
  }

  // eslint-disable-next-line react-hooks/exhaustive-deps
  const checkForRowOfFour = () => {
    for (let i = 0; i < 64; i++) {
      const rowOfFour = [i, i +1, i +2, i +3]
      const decidedColor = currentColorArrangement[i]
      const notValid = [5, 6, 7, 13, 14, 15, 21, 22, 23, 29, 30, 31, 37, 38, 39, 45, 46, 47, 53, 54, 55, 62, 63, 64]
      const isBlank = currentColorArrangement[i] === blank

      if (notValid.includes(i)) continue

      if( rowOfFour.every(squery => currentColorArrangement[squery] === decidedColor && !isBlank)) {
        setScoreDisplay((score) => score + 4)
        rowOfFour.forEach(square => currentColorArrangement[square] = blank)
        return true
      }
    }
  }

  // eslint-disable-next-line react-hooks/exhaustive-deps
  const moveIntoSquareBelow = () => {
    for (let i = 0; i <= 55; i++) {
      const firstRow = [0, 1, 2, 3, 4, 5, 6, 7]
      const isFirstRow = firstRow.includes(i)
      

      if (isFirstRow && currentColorArrangement[i] === blank) {
        let randomNumber = Math.floor(Math.random() * candyColors.length)
        currentColorArrangement[i] = candyColors[randomNumber]
      }

      if ((currentColorArrangement[i + width]) === blank ){
        currentColorArrangement[i + width] = currentColorArrangement[i]
        currentColorArrangement[i] = blank
      }
    }
  }

  console.log(scoreDisplay)

  const dragStart = (e) => {
    console.log('drag start')
    setSquireBeingDragged(e.target)
  }
  const dragDrop = (e) => {
    console.log(e.target)
    console.log('drag drop')
    setSquireBeingReplaced(e.target)
  }
  const dragEnd = () => {
    console.log('drag end')


    const squareBeingDraggedId = parseInt(squareBeingDragged.getAttribute('data-id'))
    const squareBeingReplacedId = parseInt(squareBeingReplaced.getAttribute('data-id'))

    currentColorArrangement[squareBeingReplacedId] = squareBeingDragged.getAttribute('src')
    currentColorArrangement[squareBeingDraggedId] = squareBeingReplaced.getAttribute('src')

    console.log('squareBeingDraggedId',squareBeingDraggedId)
    console.log('squareBeingReplacedId', squareBeingReplacedId)

    const validMoves = [
      squareBeingDraggedId -1,
      squareBeingDraggedId -width,
      squareBeingDraggedId -1,
      squareBeingDraggedId -width
    ]

    const validMove = validMoves.includes(squareBeingReplacedId)

    const isAColumnOfFour = checkForColumnOfFour()
    const isARowOfFour = checkForRowOfFour()
    const isAColumnOfThree = checkForColumnOfThree()
    const isARowOfThree = checkForRowOfThree()

    if (squareBeingReplacedId &&
       validMove && (isARowOfThree || isARowOfFour || isAColumnOfFour || isAColumnOfThree)) {
        setSquireBeingDragged(null)
        setSquireBeingReplaced(null)
       } else {
        currentColorArrangement[squareBeingReplacedId] = squareBeingReplaced.getAttribute('src')
        currentColorArrangement[squareBeingDraggedId] = squareBeingDragged.getAttribute('src')
        setCurrentColorArrangement([...currentColorArrangement])
       }

  }

  

  const createBord = () => {
    const randomColorArrangment = []
    for (let i = 0; i < width * width; i++) {
      const randomColor = candyColors[Math.floor(Math.random() * candyColors.length)]
      randomColorArrangment.push(randomColor)
    }
    setCurrentColorArrangement(randomColorArrangment)
  }

  useEffect(() => {
    createBord()
  }, [])

  useEffect(() => {
    const timer = setInterval(() => {
      checkForColumnOfThree()
      checkForColumnOfFour()
      checkForRowOfThree()
      checkForRowOfFour()
      moveIntoSquareBelow()
      setCurrentColorArrangement([...currentColorArrangement])
    }, 100)
    return () => clearInterval(timer)
    
  }, [checkForColumnOfThree, checkForColumnOfFour, checkForRowOfThree, checkForRowOfFour,moveIntoSquareBelow, currentColorArrangement])


  //console.log(currentColorArrangement)

  return (
    <div className="app">
      <div className="game">
        {currentColorArrangement.map((candyColor, index) => (
          // eslint-disable-next-line jsx-a11y/alt-text
          <img 
            key={index}
            src={candyColor}
            alt={candyColor}
            data-id={index}
            draggable={true}
            onDragStart={dragStart}
            onDragOver={(e) => e.preventDefault()}
            onDragEnter={(e) => e.preventDefault()}
            onDragLeave={(e) => e.preventDefault()}
            onDrop={dragDrop}
            onDragEnd={dragEnd}
          />
        ))}
      </div>
      <ScoreBoard score={scoreDisplay}/>
    </div>
  )
}

export default App;
